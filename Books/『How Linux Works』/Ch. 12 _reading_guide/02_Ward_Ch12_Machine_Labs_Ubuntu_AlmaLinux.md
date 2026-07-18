#Book/How-Linux-Works #Author/Ward 
#network
# Ward Ch. 12 Machine Labs — Ubuntu db01 + AlmaLinux app01

These labs assume:

```text
app01 = AlmaLinux app server
db01 = Ubuntu database server
john = normal non-admin user
john-admin = admin user
```

Run these labs slowly. The point is not to finish. The point is to predict, test, inspect, and explain.

---

## Lab 0 — Prepare both machines

On both machines, make sure basic tools exist.

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install -y openssh-client openssh-server rsync tree tar gzip zip unzip netcat-openbsd
```

### AlmaLinux / Rocky

```bash
sudo dnf install -y openssh-clients openssh-server rsync tree tar gzip zip unzip nc
```

Check SSH services.

### Ubuntu

```bash
systemctl status ssh
```

### AlmaLinux

```bash
systemctl status sshd
```

Check hostnames and IP addresses:

```bash
hostnamectl
ip addr
ip route
```

Create a note:

```text
app01 IP:
db01 IP:
workstation IP:
```

---

## Lab 1 — Prove SSH works first

From `db01`:

```bash
ssh john@app01 hostname
```

From `app01`:

```bash
ssh john@db01 hostname
```

If this fails, do not continue to scp or rsync. File transfer over SSH depends on SSH working.

Check logs if needed.

### Ubuntu SSH logs

```bash
sudo journalctl -u ssh -n 50
```

### AlmaLinux SSH logs

```bash
sudo journalctl -u sshd -n 50
```

Feynman question:

> Why must SSH work before scp and rsync-over-SSH work?

---

## Lab 2 — Quick copy with scp

On `db01`:

```bash
mkdir -p ~/ch12-lab/quick-copy
cd ~/ch12-lab/quick-copy
echo "hello from db01" > db01-note.txt
scp db01-note.txt john@app01:/home/john/
```

On `app01`:

```bash
ls -l /home/john/db01-note.txt
cat /home/john/db01-note.txt
stat /home/john/db01-note.txt
```

Questions:

```text
Who owns the copied file?
What are its permissions?
Did the timestamp change?
Did the contents survive correctly?
```

Now reverse direction.

On `app01`:

```bash
mkdir -p ~/ch12-lab/quick-copy
cd ~/ch12-lab/quick-copy
echo "hello from app01" > app01-note.txt
scp app01-note.txt john@db01:/home/john/
```

On `db01`:

```bash
ls -l /home/john/app01-note.txt
cat /home/john/app01-note.txt
```

---

## Lab 3 — Copy to a restricted location

On `app01`, create a root-owned destination:

```bash
sudo mkdir -p /srv/restricted-copy
sudo chown root:root /srv/restricted-copy
sudo chmod 755 /srv/restricted-copy
ls -ld /srv/restricted-copy
```

From `db01`, try:

```bash
scp ~/ch12-lab/quick-copy/db01-note.txt john@app01:/srv/restricted-copy/
```

Expected: permission denied.

Now diagnose from `app01`:

```bash
ls -ld /srv/restricted-copy
id john
```

Question:

> Why did SSH login work but file copy fail?

Correct lesson:

> Network access and filesystem permission are separate issues.

---

## Lab 4 — rsync local practice first

On either machine:

```bash
mkdir -p ~/ch12-lab/rsync-local/source
mkdir -p ~/ch12-lab/rsync-local/dest
echo "one" > ~/ch12-lab/rsync-local/source/one.txt
echo "two" > ~/ch12-lab/rsync-local/source/two.txt
```

Run:

```bash
rsync -av ~/ch12-lab/rsync-local/source/ ~/ch12-lab/rsync-local/dest/
tree ~/ch12-lab/rsync-local
```

Change one file:

```bash
echo "one changed" > ~/ch12-lab/rsync-local/source/one.txt
rsync -av ~/ch12-lab/rsync-local/source/ ~/ch12-lab/rsync-local/dest/
```

Question:

> What did rsync transfer the second time?

---

## Lab 5 — Trailing slash lab

This is the most important rsync beginner trap.

On `db01`:

```bash
mkdir -p ~/ch12-lab/slash/project
printf "alpha\n" > ~/ch12-lab/slash/project/a.txt
printf "beta\n" > ~/ch12-lab/slash/project/b.txt
```

Clean destination on `app01`:

```bash
ssh john@app01 'rm -rf ~/slash-test && mkdir -p ~/slash-test/no-slash ~/slash-test/with-slash'
```

Run without trailing slash:

```bash
rsync -av ~/ch12-lab/slash/project john@app01:/home/john/slash-test/no-slash/
```

Run with trailing slash:

```bash
rsync -av ~/ch12-lab/slash/project/ john@app01:/home/john/slash-test/with-slash/
```

Inspect:

```bash
ssh john@app01 'tree ~/slash-test'
```

He must write the result:

```text
Without trailing slash, destination contains:

With trailing slash, destination contains:
```

Feynman sentence:

> The trailing slash decides whether I copy the directory itself or the contents of the directory.

---

## Lab 6 — Dry run before real sync

On `db01`:

```bash
mkdir -p ~/ch12-lab/dryrun/source
echo "safe" > ~/ch12-lab/dryrun/source/safe.txt
rsync -avn ~/ch12-lab/dryrun/source/ john@app01:/home/john/dryrun-dest/
```

Question:

> Did anything actually copy?

Now run the real command:

```bash
rsync -av ~/ch12-lab/dryrun/source/ john@app01:/home/john/dryrun-dest/
```

Inspect:

```bash
ssh john@app01 'tree ~/dryrun-dest'
```

Rule:

> Use dry-run when the destination matters, when using `--delete`, or when the command is new.

---

## Lab 7 — Exclude files

On `db01`:

```bash
mkdir -p ~/ch12-lab/exclude/project/{src,target,.git}
echo "source" > ~/ch12-lab/exclude/project/src/main.clj
echo "compiled junk" > ~/ch12-lab/exclude/project/target/app.jar
echo "git data" > ~/ch12-lab/exclude/project/.git/config
echo "DB_PASSWORD=secret" > ~/ch12-lab/exclude/project/.env
```

Dry run:

```bash
rsync -avn \
  --exclude '.git/' \
  --exclude 'target/' \
  --exclude '.env' \
  ~/ch12-lab/exclude/project/ \
  john@app01:/home/john/exclude-project/
```

Real run:

```bash
rsync -av \
  --exclude '.git/' \
  --exclude 'target/' \
  --exclude '.env' \
  ~/ch12-lab/exclude/project/ \
  john@app01:/home/john/exclude-project/
```

Inspect:

```bash
ssh john@app01 'find ~/exclude-project -maxdepth 3 -type f -print'
```

Questions:

```text
Did .env transfer?
Did target/ transfer?
Did .git/ transfer?
Why are these often excluded?
```

---

## Lab 8 — The dangerous --delete lab

Set up:

```bash
mkdir -p ~/ch12-lab/delete/source
printf "keep\n" > ~/ch12-lab/delete/source/keep.txt
ssh john@app01 'rm -rf ~/delete-dest && mkdir ~/delete-dest && echo old > ~/delete-dest/old.txt'
```

Dry run with delete:

```bash
rsync -avn --delete ~/ch12-lab/delete/source/ john@app01:/home/john/delete-dest/
```

Question:

> Which file would be deleted?

Only after answering, run:

```bash
rsync -av --delete ~/ch12-lab/delete/source/ john@app01:/home/john/delete-dest/
```

Inspect:

```bash
ssh john@app01 'tree ~/delete-dest'
```

Lesson:

> `--delete` makes the destination match the source. That is useful and dangerous.

---

## Lab 9 — Pull a backup from db01

On `db01`:

```bash
mkdir -p ~/db-backups
echo "fake SQL backup from db01" > ~/db-backups/crudapp-$(date +%F).sql
ls -lh ~/db-backups
```

From `app01`, pull it:

```bash
mkdir -p ~/pulled-db-backups
rsync -av john@db01:/home/john/db-backups/ ~/pulled-db-backups/
ls -lh ~/pulled-db-backups
cat ~/pulled-db-backups/*.sql
```

Questions:

```text
Which machine initiated the connection?
Was this a push or pull?
Why might backup pulls be safer than backup pushes?
```

---

## Lab 10 — Push an app artifact to app01

On `db01` or workstation, simulate a Clojure app artifact:

```bash
mkdir -p ~/fake-clojure-build
echo "pretend this is a jar" > ~/fake-clojure-build/app.jar
echo "do not copy this secret" > ~/fake-clojure-build/.env
echo "deployment notes" > ~/fake-clojure-build/README.md
```

Dry run:

```bash
rsync -avn --exclude '.env' ~/fake-clojure-build/ john@app01:/home/john/app-deploy-staging/
```

Real run:

```bash
rsync -av --exclude '.env' ~/fake-clojure-build/ john@app01:/home/john/app-deploy-staging/
```

Inspect:

```bash
ssh john@app01 'find ~/app-deploy-staging -maxdepth 2 -type f -ls'
```

Question:

> Did the secret file transfer?

---

## Lab 11 — Permission mismatch after transfer

On `db01`:

```bash
mkdir -p ~/ch12-lab/perms
printf '#!/usr/bin/env bash\necho hello\n' > ~/ch12-lab/perms/run.sh
chmod 644 ~/ch12-lab/perms/run.sh
rsync -av ~/ch12-lab/perms/ john@app01:/home/john/perms-test/
```

On `app01`:

```bash
cd ~/perms-test
ls -l run.sh
./run.sh
```

Expected: permission denied.

Fix on source, then resync:

```bash
# On db01
chmod +x ~/ch12-lab/perms/run.sh
rsync -av ~/ch12-lab/perms/ john@app01:/home/john/perms-test/
```

On `app01`:

```bash
cd ~/perms-test
ls -l run.sh
./run.sh
```

Lesson:

> Transfer does not fix wrong permissions. It often faithfully preserves them.

---

## Lab 12 — Concept-only: Samba, SSHFS, NFS

Do not install all of these yet unless there is a reason. First, classify them.

Create this table in notes:

```text
Tool: scp
Type: transfer or sharing?
Best use:
Risk:

Tool: rsync
Type: transfer/sync or sharing?
Best use:
Risk:

Tool: Samba
Type: transfer or sharing?
Best use:
Risk:

Tool: SSHFS
Type: transfer or sharing?
Best use:
Risk:

Tool: NFS
Type: transfer or sharing?
Best use:
Risk:
```

Minimum acceptable answers:

```text
scp: one-time copy over SSH
rsync: repeated synchronization, backup, deployment
Samba: network file sharing, especially with Windows clients
SSHFS: mount remote files over SSH
NFS: Unix/Linux network filesystem sharing
```

---

# End lab report

Create:

```bash
mkdir -p ~/homelab-notes
vim ~/homelab-notes/ward-ch12-report.md
```

Required sections:

```md
# Ward Ch. 12 Report

## Machines used

## Commands practiced

## scp explanation

## rsync explanation

## Trailing slash example

## Exclude example

## Dangerous --delete example

## Backup pull example

## Transfer vs sharing

## Mistakes I made

## What I can now explain
```

Do not skip the mistakes section. That is where real learning is visible.
