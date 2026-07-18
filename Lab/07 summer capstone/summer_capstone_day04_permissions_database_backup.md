# Day 4 — Permissions, Database Ownership, Backup, and Restore

## Expected time

```text
Target: 120–180 minutes
Minimum: 90 minutes
Deep version: 3 hours with permission break/fix and restore-test explanation
```

## Goal

Understand data protection.

Key idea:

```text
Code can be redeployed.
Data must be protected.
```

Database:

```text
/var/lib/site6-app/site6.db
```


---

# Daily depth rule

Do not stop because the commands ran successfully.

Stop only when you can explain:

```text
What question was I asking?
What command did I run?
What exact evidence did I get?
What does it prove?
What does it not prove?
What is the next narrow question?
```

If you finish in under 60 minutes, you probably did it too shallowly. Add one extra break/fix case or rewrite your explanation more clearly.


# Part 1 — Locate data

Run:

```bash
ls -ld /var/lib/site6-app
ls -lh /var/lib/site6-app/site6.db
id site6
sudo -u site6 test -r /var/lib/site6-app/site6.db && echo site6-can-read
sudo -u site6 test -w /var/lib/site6-app/site6.db && echo site6-can-write
```

Write:

```text
Data directory owner:
Database owner:
Database size:
Read test:
Write test:
What this proves:
What this does not prove:
```

# Part 2 — Create a test record

Use the app to create a task:

```bash
curl -H 'Host: site6.local'   -X POST   -d 'title=backup proof task'   -d 'body=this should be backed up'   http://app01/tasks
```

Verify:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'backup proof task'
```

Write:

```text
Task:
Evidence:
Why this task matters:
```

# Part 3 — Backup database

Stop service for simple SQLite copy:

```bash
sudo systemctl stop site6-app
```

Backup:

```bash
sudo -u site6 sh -c '
mkdir -p /var/backups/site6-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site6-app/site6.db /var/backups/site6-app/site6-$timestamp.db
ls -lh /var/backups/site6-app/site6-$timestamp.db
'
```

Start service:

```bash
sudo systemctl start site6-app
```

Write:

```text
Backup file:
Why service was stopped:
What this backup protects:
What this backup does not protect:
```

# Part 4 — Restore test into /tmp

Do not overwrite live data.

```bash
sudo rm -rf /tmp/site6-restore-test
sudo mkdir -p /tmp/site6-restore-test
sudo chown site6:site6 /tmp/site6-restore-test

sudo -u site6 sh -c '
latest=$(ls -t /var/backups/site6-app/site6-*.db | head -n 1)
echo "$latest"
cp "$latest" /tmp/site6-restore-test/site6.db
ls -lh /tmp/site6-restore-test/site6.db
'
```

If sqlite3 is installed:

```bash
sudo -u site6 sqlite3 /tmp/site6-restore-test/site6.db 'SELECT id, title, done FROM tasks;'
```

Write:

```text
Restore-test file:
Restore-test evidence:
What this proves:
What this does not prove:
```

# Part 5 — Controlled failure: bad ownership

Break ownership:

```bash
sudo chown root:root /var/lib/site6-app
```

Restart and test POST:

```bash
sudo systemctl restart site6-app
curl -v -H 'Host: site6.local'   -X POST   -d 'title=permission failure'   -d 'body=should fail or error'   http://app01/tasks
```

Check:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
ls -ld /var/lib/site6-app
```

Fix:

```bash
sudo chown -R site6:site6 /var/lib/site6-app
sudo systemctl restart site6-app
```

Verify:

```bash
curl -H 'Host: site6.local'   -X POST   -d 'title=permission fixed'   -d 'body=works now'   http://app01/tasks

curl -s -H 'Host: site6.local' http://app01/ | grep 'permission fixed'
```

Write:

```text
Failure:
Symptom:
Evidence:
Fix:
Verification:
```

# Deliverable

Create or improve:

```text
BACKUP_RESTORE.md
```

Required sections:

```text
# Backup and Restore

## What data must be protected
## Live database path
## Backup directory
## Backup procedure
## Restore-test procedure
## Real restore procedure
## Risks of restore
## How to verify data survived
```

# Completion checklist

```text
[ ] I located the database.
[ ] I verified permissions.
[ ] I created a backup.
[ ] I tested restore into /tmp.
[ ] I broke and fixed ownership.
[ ] I updated BACKUP_RESTORE.md.
```
