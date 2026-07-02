# Ward Ch. 12 Reading Guide — Network File Transfer and Sharing

> Correction: it is usually spelled **Feynman method**, not “Feinman method.”

## Purpose of this chapter

Do not read this chapter as “a list of file-copying tools.” That is too shallow.

Ward is trying to teach a larger infrastructure idea:

> Once files move across machines, you must think about identity, trust, ownership, permissions, consistency, performance, and recovery.

A beginner thinks:

> “How do I copy this file?”

An infrastructure person thinks:

> “Who owns the file after transfer? Did permissions survive? Is the copy complete? Can I repeat it? Is it secure? Is it a one-time transfer or a shared filesystem? What breaks if the network is slow or disconnected?”

That is the real lesson of Chapter 12.

---

## Recommended lab machines

Use the existing home lab:

```text
app01 = AlmaLinux / Rocky-style app server
  Role: Clojure CRUD app server

db01 = Ubuntu / Debian-style database server
  Role: MySQL or MariaDB server

workstation = his daily machine
  Role: where he types commands from
```

Example IPs used in this guide:

```text
app01: 192.168.1.50
db01:  192.168.1.51
```

Adjust to the real IP addresses.

---

## Prerequisites before reading Ch. 12

He should already have basic comfort with:

```text
ssh
users and groups
file permissions
chmod / chown / chgrp
systemctl
journalctl
ip addr
ss -tulpen
basic TCP ports
basic shell redirection
basic tar/gzip
```

If he cannot explain `chmod 644`, `chmod 755`, and `ssh john@app01`, he is not ready to get full value from this chapter.

---

## Chapter 12 reading map

Use this order while reading.

### Part 1 — Quick copy

Focus question:

> When is a simple copy enough?

Commands to connect:

```bash
scp
sftp
ssh
```

He should understand:

```text
source path
destination path
remote username
remote hostname
remote directory
how SSH protects the transfer
why copying as the wrong user creates permission problems
```

Feynman checkpoint:

> Explain `scp file.txt john@app01:/home/john/` to a younger student without using the word “magic.”

---

### Part 2 — rsync

Focus question:

> Why is rsync more serious than simple copying?

He should understand:

```text
incremental transfer
directory synchronization
trailing slash behavior
archive mode
verbose mode
dry-run mode
exclude patterns
compression
bandwidth limits
pull vs push
```

Feynman checkpoint:

> Explain the difference between copying a folder and syncing a folder.

---

### Part 3 — Exact copies and trailing slashes

Focus question:

> What exactly will exist at the destination after the command runs?

This is where beginners make mistakes.

He must be able to predict the result of:

```bash
rsync -av project john@app01:/home/john/backups/
rsync -av project/ john@app01:/home/john/backups/
```

Feynman checkpoint:

> Draw the destination directory tree before running the command.

If he refuses to predict first, he is not learning. He is gambling.

---

### Part 4 — Excludes and safeguards

Focus question:

> How do you avoid copying junk, secrets, or build artifacts?

He should test excludes for:

```text
.git/
target/
node_modules/
*.log
.env
*.sql
```

Feynman checkpoint:

> Why is `--dry-run` not optional when learning rsync?

---

### Part 5 — Sharing vs transferring

Focus question:

> Is this a one-time copy, repeated sync, or a shared filesystem?

He should distinguish:

```text
scp/sftp = simple file transfer
rsync = efficient repeated synchronization
Samba = file sharing, often for Windows compatibility
SSHFS = mount remote files over SSH
NFS = Unix/Linux network filesystem sharing
cloud storage = remote managed storage with different tradeoffs
```

Feynman checkpoint:

> When would you use rsync instead of Samba? When would Samba be reasonable?

---

## Reading schedule

### Day 1 — Quick copy and mental model

Read the quick-copy section.

Do these experiments:

```bash
scp small files between machines
copy a directory
copy to a nonexistent path
copy to a path without permission
inspect ownership and permissions after copying
```

Write a short explanation:

> “A file transfer is not only bytes moving. It also involves users, paths, permissions, and trust.”

---

### Day 2 — rsync basics

Read the start of the rsync section.

Do these experiments:

```bash
rsync -av source/ destination/
rsync -av source john@app01:/home/john/rsync-lab/
rsync -av source/ john@app01:/home/john/rsync-lab/
rsync -avn source/ john@app01:/home/john/rsync-lab/
```

Focus on predicting results first.

---

### Day 3 — rsync safety

Read excludes, safeguards, verbose mode, compression, bandwidth limiting.

Do these experiments:

```bash
rsync --dry-run
rsync --exclude
rsync --delete --dry-run
rsync -z
rsync --bwlimit
```

He should write:

> “What could go wrong if I use `--delete` carelessly?”

---

### Day 4 — transfer project files

Use his Clojure CRUD app.

Practice copying:

```text
built JAR file
static assets
config template
README or deployment notes
```

Do not copy:

```text
.env with secrets
.git directory unless intentionally needed
target/ build junk unless it is the intended artifact
local database files
```

---

### Day 5 — sharing concepts

Read Samba, SSHFS, NFS, cloud storage sections at a conceptual level.

He does not need to deploy all of them yet.

He should answer:

```text
What problem does Samba solve?
What problem does NFS solve?
What problem does SSHFS solve?
Why might a production system avoid casual file sharing?
Why is shared storage a security and reliability concern?
```

---

## End-of-chapter standard

He is done with Ch. 12 only when he can do these without guessing:

```text
copy a file between app01 and db01
copy a directory without accidentally nesting it incorrectly
use rsync dry-run before a real sync
exclude unwanted files
explain trailing slash behavior
check ownership and permissions after transfer
pull a database backup from db01
push a built app artifact to app01
explain transfer vs sharing
explain why chmod 777 is not a fix
```

If he cannot explain these, he has read the chapter but not learned it.
