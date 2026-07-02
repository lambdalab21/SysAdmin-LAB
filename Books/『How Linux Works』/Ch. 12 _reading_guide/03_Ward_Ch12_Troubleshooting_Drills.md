# Ward Ch. 12 Troubleshooting Drills

These drills are designed to break file transfers in controlled ways.

Do not guess. Diagnose.

Use this format for every drill:

```text
Symptom:
Command I ran:
What I expected:
What happened:
Evidence:
Root cause:
Fix:
Prevention:
```

---

## Drill 1 — SSH works by IP but not hostname

### Break it

From `db01`, try:

```bash
ssh john@app01 hostname
```

If hostname fails, test IP:

```bash
ssh john@192.168.1.50 hostname
```

### Diagnose

```bash
getent hosts app01
cat /etc/hosts
resolvectl status 2>/dev/null || cat /etc/resolv.conf
ping -c 2 app01
ping -c 2 192.168.1.50
```

### Feynman question

> Why can IP connectivity work while hostname resolution fails?

### Fix options

Add to `/etc/hosts` on both machines:

```text
192.168.1.50 app01
192.168.1.51 db01
```

Use:

```bash
sudoedit /etc/hosts
```

---

## Drill 2 — Permission denied copying into /srv

### Break it

On `app01`:

```bash
sudo mkdir -p /srv/ch12-drill
sudo chown root:root /srv/ch12-drill
sudo chmod 755 /srv/ch12-drill
```

From `db01`:

```bash
echo test > ~/test.txt
scp ~/test.txt john@app01:/srv/ch12-drill/
```

### Diagnose

On `app01`:

```bash
ls -ld /srv/ch12-drill
id john
```

### Feynman question

> Why did authentication succeed but writing the file fail?

### Fix options

Bad lazy fix:

```bash
sudo chmod 777 /srv/ch12-drill
```

Do not use that.

Better owner fix:

```bash
sudo chown john:john /srv/ch12-drill
sudo chmod 755 /srv/ch12-drill
```

Better group fix:

```bash
sudo groupadd deployers
sudo usermod -aG deployers john
sudo chown root:deployers /srv/ch12-drill
sudo chmod 2775 /srv/ch12-drill
```

Then log out and back in.

---

## Drill 3 — rsync copied one directory too deep

### Break it

On `db01`:

```bash
mkdir -p ~/bad-slash/project
printf "hello\n" > ~/bad-slash/project/file.txt
rsync -av ~/bad-slash/project john@app01:/home/john/bad-slash-dest/
```

### Inspect

```bash
ssh john@app01 'tree ~/bad-slash-dest'
```

### Diagnose

Question:

```text
Did I want bad-slash-dest/project/file.txt?
Or did I want bad-slash-dest/file.txt?
```

### Fix

Use the correct source path:

```bash
rsync -av ~/bad-slash/project/ john@app01:/home/john/bad-slash-dest/
```

Feynman sentence:

> Without trailing slash, rsync copies the directory. With trailing slash, rsync copies the directory contents.

---

## Drill 4 — Exclude pattern failed

### Break it

On `db01`:

```bash
mkdir -p ~/exclude-fail/project/{src,target}
echo code > ~/exclude-fail/project/src/main.clj
echo junk > ~/exclude-fail/project/target/app.jar
```

Run an incorrect exclude:

```bash
rsync -av --exclude '/wrong-target/' ~/exclude-fail/project/ john@app01:/home/john/exclude-fail/
```

### Inspect

```bash
ssh john@app01 'find ~/exclude-fail -maxdepth 3 -type f -print'
```

### Fix

```bash
rsync -av --delete --exclude 'target/' ~/exclude-fail/project/ john@app01:/home/john/exclude-fail/
```

Use dry-run first if unsure:

```bash
rsync -avn --delete --exclude 'target/' ~/exclude-fail/project/ john@app01:/home/john/exclude-fail/
```

Feynman question:

> Where is the exclude pattern evaluated relative to the source directory?

---

## Drill 5 — `--delete` removed a file

### Break it safely

On `app01`:

```bash
mkdir -p ~/delete-drill
printf "important but only on destination\n" > ~/delete-drill/destination-only.txt
```

On `db01`:

```bash
mkdir -p ~/delete-source
printf "source file\n" > ~/delete-source/source.txt
rsync -avn --delete ~/delete-source/ john@app01:/home/john/delete-drill/
```

### Predict

Before real run:

```text
Which file would be deleted?
Why?
```

### Run only after prediction

```bash
rsync -av --delete ~/delete-source/ john@app01:/home/john/delete-drill/
```

### Lesson

> `--delete` means the destination should mirror the source. Anything extra at the destination can disappear.

---

## Drill 6 — SSH key permissions break login

### Break it

On `app01` as `john`:

```bash
chmod 777 ~/.ssh
```

Try to SSH in from `db01`.

### Diagnose

On `app01`:

```bash
ls -ld ~/.ssh
ls -l ~/.ssh
```

Check logs:

```bash
sudo journalctl -u sshd -n 100
```

On Ubuntu, the service is usually:

```bash
sudo journalctl -u ssh -n 100
```

### Fix

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Feynman question

> Why does SSH care about file permissions?

---

## Drill 7 — rsync command points to wrong destination

### Break it

Run a command that creates a surprising destination:

```bash
rsync -av ~/delete-source/ john@app01:backup
```

### Diagnose

On `app01`:

```bash
pwd
ls -la ~
tree ~/backup
```

### Question

```text
What does john@app01:backup mean?
Is backup absolute or relative?
Where did the files go?
```

### Safer habit

Use absolute paths when learning:

```bash
rsync -av ~/delete-source/ john@app01:/home/john/backup/
```

---

## Drill 8 — Transfer succeeds but app cannot read file

### Setup

On `app01`:

```bash
sudo useradd --system --create-home --home-dir /opt/clojure-crud --shell /sbin/nologin clojureapp 2>/dev/null || true
sudo mkdir -p /opt/clojure-crud/app
sudo chown -R clojureapp:clojureapp /opt/clojure-crud
```

From `db01`, copy as `john`:

```bash
echo "fake jar" > ~/app.jar
scp ~/app.jar john@app01:/home/john/app.jar
```

On `app01`, move it as root but leave wrong ownership:

```bash
sudo mv /home/john/app.jar /opt/clojure-crud/app/app.jar
ls -l /opt/clojure-crud/app/app.jar
```

### Diagnose

```bash
sudo -u clojureapp test -r /opt/clojure-crud/app/app.jar && echo readable || echo not-readable
namei -l /opt/clojure-crud/app/app.jar
```

### Fix

```bash
sudo chown clojureapp:clojureapp /opt/clojure-crud/app/app.jar
sudo chmod 640 /opt/clojure-crud/app/app.jar
```

### Lesson

> File transfer is only one step. The service account must be able to read what it needs.

---

## Drill 9 — Firewall confusion

This drill connects Chapter 10 and Chapter 12.

### Symptom

`scp` or `rsync` fails with connection timeout.

### Diagnose from source machine

```bash
ping -c 2 app01
nc -vz app01 22
ssh -v john@app01 hostname
```

### Diagnose on destination

Ubuntu:

```bash
sudo ufw status verbose
sudo systemctl status ssh
```

AlmaLinux:

```bash
sudo firewall-cmd --list-all
sudo systemctl status sshd
```

### Feynman question

> What is the difference between SSH refusing login and the network blocking TCP port 22?

---

## Drill 10 — Backup copied but not verified

### Bad behavior

```bash
rsync -av john@db01:/home/john/db-backups/ ~/db-backups/
```

Then stop.

That is not enough.

### Better behavior

```bash
ls -lh ~/db-backups
wc -c ~/db-backups/*
sha256sum ~/db-backups/*
head -n 5 ~/db-backups/*
```

If it is a real SQL dump later:

```bash
mysql --help >/dev/null
```

Eventually test restore into a throwaway database.

### Lesson

> A backup transfer is not proven until the backup is checked and eventually restored.

---

# Final troubleshooting exam

Give him these symptoms and make him diagnose without hints:

```text
1. scp says Permission denied.
2. ssh works by IP but not hostname.
3. rsync created project/project/file.txt.
4. rsync copied .env by accident.
5. rsync --delete removed destination-only files.
6. SSH key login stopped working after chmod 777 ~/.ssh.
7. The app cannot read the copied jar.
8. Backup file copied but is zero bytes.
9. nc app01 22 times out.
10. A copied script will not execute.
```

For each one, he must write:

```text
likely cause
commands to confirm
fix
prevention
```

No guessing. Evidence first.
