# 055d — Backup, Restore, and Safe Deploy for Tiny Message Board

## Purpose

Protect the data while deploying code.

The database is valuable:

```text
/var/lib/site55-app/site55.db
```

The code is replaceable:

```text
/opt/site55-app/app.py
```

Main rule:

```text
Deploy code.
Do not delete data.
```

---

# Part 1 — Prove current data exists

Add a message:

```bash
curl -H 'Host: site55.local' -X POST -d 'body=backup and deploy survival note' http://app01/messages
```

Verify:

```bash
curl -s -H 'Host: site55.local' http://app01/ | grep 'backup and deploy survival note'
```

Write:

```text
Message:

Evidence:

Why this message matters:
```

Expected:

```text
This message will prove whether data survives backup, restart, and deployment.
```

---

# Part 2 — Locate database and permissions

On `app01`:

```bash
ls -lh /var/lib/site55-app/site55.db
ls -ld /var/lib/site55-app
sudo -u site55 test -r /var/lib/site55-app/site55.db && echo site55-can-read
sudo -u site55 test -w /var/lib/site55-app/site55.db && echo site55-can-write
```

Write:

```text
Database file:

Database owner:

Directory owner:

Read evidence:

Write evidence:

What this proves:
```

---

# Part 3 — Create a backup

Create backup directory:

```bash
sudo mkdir -p /var/backups/site55-app
sudo chown site55:site55 /var/backups/site55-app
```

Stop app before simple file copy:

```bash
sudo systemctl stop site55-app
```

Copy database:

```bash
sudo -u site55 sh -c '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site55-app/site55.db /var/backups/site55-app/site55-$timestamp.db
ls -lh /var/backups/site55-app/site55-$timestamp.db
'
```

Start app:

```bash
sudo systemctl start site55-app
```

Write:

```text
Backup file:

Why app was stopped:

What this backup protects:

What this backup does not protect:
```

---

# Part 4 — Restore test into /tmp

Do not overwrite live data.

```bash
sudo rm -rf /tmp/site55-restore-test
sudo mkdir -p /tmp/site55-restore-test
sudo chown site55:site55 /tmp/site55-restore-test

sudo -u site55 sh -c '
latest=$(ls -t /var/backups/site55-app/site55-*.db | head -n 1)
echo "$latest"
cp "$latest" /tmp/site55-restore-test/site55.db
ls -lh /tmp/site55-restore-test/site55.db
'
```

If `sqlite3` is installed:

```bash
sudo -u site55 sqlite3 /tmp/site55-restore-test/site55.db 'SELECT id, body, created_at FROM messages;'
```

Write:

```text
Restore-test file:

Restore-test size:

Data inspection evidence:

What this proves:

What this does not prove:
```

Expected:

```text
Backup exists and can be copied.
If inspected with sqlite3, it contains messages.
This does not automatically prove live restore was done.
```

---

# Part 5 — Safe code change

Locally:

```bash
cd ~/projects/site55-message-board
vi app.py
```

Change heading:

```html
<h1>site55 message board - safe deploy</h1>
```

Verify:

```bash
grep -n 'safe deploy' app.py
```

If using Git:

```bash
git status
git diff
git add app.py
git commit -m "Update site55 heading"
```

Write:

```text
Code change:

What should change:

What should not change:
```

---

# Part 6 — Dry run deployment

From local project:

```bash
rsync -avn --delete ./ deploy@app01:/opt/site55-app/
```

Stop.

Inspect output.

Write:

```text
Source:

Destination:

Files that would change:

Files that would be deleted:

Does the output mention /var/lib/site55-app?

Does the output mention site55.db?

Is it safe to continue?
```

Expected:

```text
Only /opt/site55-app should be affected.
The database should not appear.
```

---

# Part 7 — Deploy code

Only if dry run is safe:

```bash
rsync -av --delete ./ deploy@app01:/opt/site55-app/
sudo systemctl restart site55-app
```

Verify service:

```bash
sudo systemctl status site55-app
```

Verify code change:

```bash
curl -s -H 'Host: site55.local' http://app01/ | grep 'safe deploy'
```

Verify data survived:

```bash
curl -s -H 'Host: site55.local' http://app01/ | grep 'backup and deploy survival note'
```

Write:

```text
Deploy command:

Service evidence:

Code-change evidence:

Data-survival evidence:

Final conclusion:
```

---

# Part 8 — Real restore procedure, only if needed

A real restore can remove newer messages.

Use intentionally.

```bash
sudo systemctl stop site55-app

sudo -u site55 sh -c '
latest=$(ls -t /var/backups/site55-app/site55-*.db | head -n 1)
cp "$latest" /var/lib/site55-app/site55.db
'

sudo systemctl start site55-app
```

Verify:

```bash
curl -s -H 'Host: site55.local' http://app01/
```

Write:

```text
Backup restored:

What data returned:

What newer data may have been lost:

Why restore is powerful and dangerous:
```

---

# Final reflection

```text
Task:

Live database:

Backup file:

Restore-test evidence:

Code deployment dry-run evidence:

Deployment command:

Code-change evidence:

Data-survival evidence:

What rsync --delete affected:

What rsync --delete did not affect:

One mistake I avoided:
```

Completion checkpoint:

```text
[ ] I can back up the SQLite database.
[ ] I can test restore into /tmp.
[ ] I can deploy code without deleting data.
[ ] I can prove old data survived.
[ ] I can explain why real restore can remove newer data.
```
