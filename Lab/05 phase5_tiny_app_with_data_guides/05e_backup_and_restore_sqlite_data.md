#LAB #deployment #backend
# 05e — Backup and Restore SQLite Data

## Purpose

Protect the database:

```text
/var/lib/site5-app/site5.db
```

Main lesson:

```text
A backup you have not tested is only a hope.
```

---

# Part 1 — Prove app has data

```bash
curl -H 'Host: site5.local' -X POST -d 'body=backup practice note' http://app01/notes
curl -s -H 'Host: site5.local' http://app01/ | grep 'backup practice note'
```

Write:

```text
Note added:
HTTP evidence:
What this proves:
```

---

# Part 2 — Locate database

```bash
ls -lh /var/lib/site5-app/site5.db
sudo -u site5 test -r /var/lib/site5-app/site5.db && echo site5-can-read-db
sudo -u site5 test -w /var/lib/site5-app/site5.db && echo site5-can-write-db
```

Write:

```text
Database path:
Size:
Owner:
Read test:
Write test:
What this proves:
```

---

# Part 3 — Create backup directory

```bash
sudo mkdir -p /var/backups/site5-app
sudo chown site5:site5 /var/backups/site5-app
ls -ld /var/backups/site5-app
```

Write:

```text
Backup directory:
Owner:
What this proves:
```

---

# Part 4 — Make timestamped backup

For this learning stage, stop the app briefly before copying:

```bash
sudo systemctl stop site5-app
sudo -u site5 sh -c '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site5-app/site5.db /var/backups/site5-app/site5-$timestamp.db
ls -lh /var/backups/site5-app/site5-$timestamp.db
'
sudo systemctl start site5-app
sudo systemctl status site5-app
```

Stop and answer:

```text
Why stop the app before copying?
What file was copied?
Where is the backup?
Why use a timestamp?
```

---

# Part 5 — Verify backup exists

```bash
sudo -u site5 ls -lh /var/backups/site5-app
```

Optional if installed:

```bash
sudo -u site5 sqlite3 /var/backups/site5-app/NAME-OF-BACKUP.db 'SELECT id, body FROM notes;'
```

Write:

```text
Backup file:
Backup size:
What this proves:
What this does not prove:
```

Expected:

```text
It proves a backup file exists.
It does not fully prove restore works.
```

---

# Part 6 — Restore test into /tmp

Do not overwrite live data yet.

```bash
sudo rm -rf /tmp/site5-restore-test
sudo mkdir -p /tmp/site5-restore-test
sudo chown site5:site5 /tmp/site5-restore-test
sudo -u site5 sh -c '
latest=$(ls -t /var/backups/site5-app/site5-*.db | head -n 1)
echo "$latest"
cp "$latest" /tmp/site5-restore-test/site5.db
ls -lh /tmp/site5-restore-test/site5.db
'
```

Optional:

```bash
sudo -u site5 sqlite3 /tmp/site5-restore-test/site5.db 'SELECT id, body FROM notes;'
```

Write:

```text
Restore-test file:
Restore-test size:
Data inspection evidence:
What this proves:
What this does not prove:
```

---

# Part 7 — Optional real restore procedure

Only do this intentionally.

```bash
sudo systemctl stop site5-app
sudo -u site5 sh -c '
latest=$(ls -t /var/backups/site5-app/site5-*.db | head -n 1)
cp "$latest" /var/lib/site5-app/site5.db
'
sudo systemctl start site5-app
curl -s -H 'Host: site5.local' http://app01/
```

Important:

```text
Restoring an older backup can remove data created after that backup.
```

---

# Final reflection

```text
Live database path:
Backup directory:
Backup file:
Command that stopped app:
Command that copied database:
Restore-test evidence:
What backup protects:
What backup does not protect:
Why restore can lose newer data:
```
