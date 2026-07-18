# 06e — Backup, Restore, and Safe Deployment for Flask CRUD

## Purpose

Protect the SQLite database while deploying code changes.

Main rule:

```text
Code deployment must not destroy data.
```

---

# Part 1 — Prove current data exists

Through Nginx, create a task:

```bash
curl -H 'Host: site6.local'   -X POST   -d 'title=backup survival task'   -d 'body=this task should survive deployment'   http://app01/tasks
```

Verify:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'backup survival task'
```

---

# Part 2 — Locate database

On app01:

```bash
ls -lh /var/lib/site6-app/site6.db
ls -ld /var/lib/site6-app
sudo -u site6 test -r /var/lib/site6-app/site6.db && echo site6-can-read
sudo -u site6 test -w /var/lib/site6-app/site6.db && echo site6-can-write
```

Write:

```text
Database file:
Database owner:
Database size:
Read evidence:
Write evidence:
What this proves:
```

---

# Part 3 — Create database backup

Stop app for a simple consistent copy:

```bash
sudo systemctl stop site6-app
```

Copy database:

```bash
sudo -u site6 sh -c '
set -e
mkdir -p /var/backups/site6-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site6-app/site6.db /var/backups/site6-app/site6-$timestamp.db
ls -lh /var/backups/site6-app/site6-$timestamp.db
'
```

Start app:

```bash
sudo systemctl start site6-app
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

Do not overwrite the live database yet.

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

---

# Part 5 — Make a code change

Locally:

```bash
cd ~/projects/site6-crud
vi templates/base.html
```

Change heading:

```html
site6 CRUD - safe deploy
```

If using Git:

```bash
git status
git diff
git add templates/base.html
git commit -m "Update site6 heading"
```

---

# Part 6 — Dry-run code deployment

```bash
rsync -avn --delete   --exclude '.git'   --exclude '.venv'   --exclude 'data'   ./ deploy@app01:/opt/site6-app/
```

Stop.

Write:

```text
Source:
Destination:
Files that would change:
Files that would be deleted:
Does output mention /var/lib/site6-app?
Does output mention site6.db?
Is it safe to continue?
```

---

# Part 7 — Deploy code

Only after safe dry run:

```bash
rsync -av --delete   --exclude '.git'   --exclude '.venv'   --exclude 'data'   ./ deploy@app01:/opt/site6-app/
```

Restart:

```bash
sudo systemctl restart site6-app
sudo systemctl status site6-app
```

Verify code change:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'safe deploy'
```

Verify old data survived:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'backup survival task'
```

Check logs:

```bash
sudo tail -n 20 /var/log/nginx/site6.access.log
sudo tail -n 20 /var/log/nginx/site6.error.log
sudo journalctl -u site6-app --since "10 minutes ago"
```

---

# Final reflection

```text
Live database:
Backup file:
Restore-test evidence:
Dry-run deployment evidence:
Code deployment command:
Code-change evidence:
Data-survival evidence:
What Git protected:
What backup protected:
What rsync affected:
What rsync did not affect:
One mistake I avoided:
```

Completion checkpoint:

```text
[ ] I can back up SQLite data.
[ ] I can test restore into /tmp.
[ ] I can deploy code safely.
[ ] I can prove old data survived.
[ ] I can explain why Git is not a database backup.
```
