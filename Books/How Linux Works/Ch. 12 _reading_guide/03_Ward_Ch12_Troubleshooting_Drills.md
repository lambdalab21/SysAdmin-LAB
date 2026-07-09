# Ward Ch. 12 Troubleshooting Drills

These drills are designed to break file transfers in controlled ways.

---

## Drill 1 — SSH works by IP but not hostname

### Break it

From `db01`, try:

```bash
ssh john@app01 hostname
```
### Diagnose

```bash
getent hosts app01
cat /etc/hosts
resolvectl status 2>/dev/null || cat /etc/resolv.conf
ping -c 2 app01
ping -c 2 192.168.1.50
```

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

---
# Final troubleshooting exam

Give him these symptoms and make him diagnose without hints:

```text
1. scp says Permission denied. Wrong permissions on target directory or file. Check with 1s-1d. Fix ownership and permissions with chown/chmod. Prevent by verifying access before transfers. 
2. ssh works by IP but not hostname. DNS or `/etc/hosts` missing early. Confirm the `getent hosts` or `ping`. Add entry to `etc/hosts` on client. Prevent by maintaining consistent host files. 
3. rsync created project/project/file.txt. Trailing slash missing on source. Confirm with tree. Use rsync ... source/dest/. Prevent by testing with -n. 
4. rsync copied .env by accident. Missing or wrong --exclude pattern. Confirm with find. add correct --exclude `.env` and use dry-run. Prevent by always previewing first. 
5. rsync --delete removed destination-only files. --delete removes files not present in the source. 
6. SSH key login stopped working after chmod 777 ~/.ssh. The directory is too permissive.  
7. The app cannot read the copied jar. Wrong ownership and permissions on the target file. 
8. Backup file copied but is zero bytes. Interrupted transfer or source file issue. 
9. nc app01 22 times out. Firewall blocking on port 22. 
10. A copied script will not execute. No execute permission. 
```