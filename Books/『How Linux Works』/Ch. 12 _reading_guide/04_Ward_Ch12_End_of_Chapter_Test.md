# Ward Ch. 12 End-of-Chapter Test — Network File Transfer and Sharing

This test is not about memorizing commands. It checks whether he understands what file transfer and sharing mean in a real Linux environment.

---

## Part 1 — Concept questions

Answer in complete sentences.

1. What is the difference between copying a file and sharing a filesystem?
2. Why is `scp` simpler than `rsync`?
3. Why is `rsync` usually better than `scp` for repeated deployments?
4. What does `rsync --dry-run` do?
5. Why is `rsync --delete` dangerous?
6. What does a trailing slash change in rsync source paths?
7. Why should `.env` usually not be copied casually?
8. Why should you inspect ownership after transferring files?
9. What problem does Samba solve?
10. What problem does NFS solve?
11. What problem does SSHFS solve?
12. Why is cloud storage not the same thing as a normal Linux filesystem?
13. Why is file sharing often a security concern?
14. Why is a backup not proven just because a file was copied?
15. Why is `chmod 777` usually the wrong response to file-transfer problems?

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

---

### Question 2

Command:

```bash
rsync -av /home/john/project/ john@app01:/home/john/deploy/
```

What will the destination tree look like?

---

### Question 3

Command:

```bash
rsync -av --exclude 'target/' /home/john/project/ john@app01:/home/john/deploy/
```

What will be excluded?

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

---

## Part 3 — Command writing

Write commands for each task.

1. Copy `notes.txt` from current machine to `/home/john/` on `app01`.
2. Copy the directory `project/` to `app01` so the destination becomes `/home/john/project/`.
3. Sync the contents of `project/` into `/home/john/deploy/` on `app01`.
4. Dry-run a sync before actually doing it.
5. Sync `project/` while excluding `.git/`, `target/`, and `.env`.
6. Pull all files from `/home/john/db-backups/` on `db01` into `~/db-backups-from-db01/` on the local machine.
7. Show the ownership and permissions of a copied file.
8. Check whether SSH port 22 is reachable on `app01`.
9. Show the last 50 SSH logs on Ubuntu.
10. Show the last 50 SSH logs on AlmaLinux.

---

## Part 4 — Troubleshooting scenarios

For each scenario, write:

```text
likely cause:
commands to check:
fix:
prevention:
```

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

---

## Part 5 — Oral Feynman explanations

Explain these out loud.

1. “A file transfer involves identity, path, permission, and trust.”
2. “rsync is not just faster scp.”
3. “A trailing slash can change the destination tree.”
4. “Dry-run is a professional habit.”
5. “File sharing is not backup.”
6. “A service account should own or read app files, not root or a random human user.”
7. “Network success and filesystem permission are separate layers.”
8. “The destination must be inspected after transfer.”

---

## Passing standard

He passes only if he can:

```text
predict rsync destination structure
use dry-run correctly
avoid copying secrets
explain ownership after transfer
troubleshoot permission denied without chmod 777
pull a backup from db01
push an app artifact to app01
explain transfer vs sharing
```

If he gets the commands right but cannot explain them, he has not passed.
