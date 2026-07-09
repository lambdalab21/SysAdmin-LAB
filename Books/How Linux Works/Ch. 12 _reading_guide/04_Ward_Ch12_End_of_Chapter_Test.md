# Ward Ch. 12 End-of-Chapter Test — Network File Transfer and Sharing

---

## Part 1 — Concept questions

Answer in complete sentences.

1. What is the difference between copying a file and sharing a filesystem? copying duplicates the files and sharing makes the same filesystems visible across machines. 
2. Why is `scp` simpler than `rsync`? scp is simpler because it is a basic one-shot secure copy. 
3. Why is `rsync` usually better than `scp` for repeated deployments? rsync is better for repeated deployments. It transfers changes the best. 
4. What does `rsync --dry-run` do? It shows what would happen without making changes. 
5. Why is `rsync --delete` dangerous? This is dangerous because it removes files on destinations that are missing from the source.
6. What does a trailing slash change in rsync source paths? Trailing slash on source means "copy contents of directory" instead of "copy the directory itself". 
7. Why should `.env` usually not be copied casually? .env files contain secrets that should not be copied to the servers. 
8. Why should you inspect ownership after transferring files? Ownership must be inspected because copied files keep the original owner which services may not access. 
9. What problem does Samba solve? Samba solves Windows-copatible file sharing. 
10. What problem does NFS solve? NFS solves native linux filesystem sharing. 
11. What problem does SSHFS solve? SSHFS solves mounting remote directories over SSH. 
12. Why is cloud storage not the same thing as a normal Linux filesystem? Cloud storage adds latency, visioning, and different permission models. 
13. Why is file sharing often a security concern? File sharing expands attack surface and permission complexity. 
14. Why is a backup not proven just because a file was copied? Backup must be verified as restorable, not just copied. 
15. Why is `chmod 777` usually the wrong response to file-transfer problems? Chmod 777 exposes files to everyone and is insecure. 

---

## Part 2 — Predict the result

Assume this source tree on `db01`:

```text
/home/john/project/
├── README.md
├── src/main.clj
└── target/app.jar
```

And this destination exists on `app01`:

```text
/home/john/deploy/
```

### Question 1

Command:

```bash
rsync -av /home/john/project john@app01:/home/john/deploy/
```

What will the destination tree look like?
/home/john/deploy/project/README.md,
/home/john/deploy/projects/src/main.clj,
/home/john/deploy/project/target/app.jar.

---

### Question 2

Command:

```bash
rsync -av /home/john/project/ john@app01:/home/john/deploy/
```

What will the destination tree look like?
Contents of project/ directly in /home/john/deploy/ (no extra project/ level).

---

### Question 3

Command:

```bash
rsync -av --exclude 'target/' /home/john/project/ john@app01:/home/john/deploy/
```

What will be excluded?
target/ directory and its contents are excluded. 

---

### Question 4

Destination currently has:

```text
/home/john/deploy/old.txt
/home/john/deploy/README.md
```

Source has only:

```text
/home/john/project/README.md
```

Command:

```bash
rsync -av --delete /home/john/project/ john@app01:/home/john/deploy/
```

What happens to `old.txt`?
old.txt is deleted. 

---

## Part 3 — Troubleshooting scenarios

### Scenario 1

`scp file.txt john@app01:/srv/app/` fails with permission denied.

### Scenario 2

`ssh john@app01` fails, but `ssh john@192.168.1.50` works.

### Scenario 3

After deployment, the app cannot read `/opt/clojure-crud/app/app.jar`.

### Scenario 4

`rsync` copied `.env` to the server by accident.

### Scenario 5

A script copied to the server gives `Permission denied` when run as `./script.sh`.

### Scenario 6

`rsync --delete` removed a file that existed only on the server.

### Scenario 7

SSH key login stopped working after changing permissions in `~/.ssh`.

### Scenario 8

A copied backup file exists, but it is empty.