#LAB #deployment #backend
# 05f — Safe Code Deployment Without Destroying Data

## Purpose

Deploy new app code while protecting SQLite data.

Rule:

```text
Deploy code.
Do not deploy over data.
```

Code:

```text
/opt/site5-app/
```

Data:

```text
/var/lib/site5-app/
```

---

# Part 1 — Create data that must survive

```bash
curl -H 'Host: site5.local' -X POST -d 'body=data survival test note' http://app01/notes
curl -s -H 'Host: site5.local' http://app01/ | grep 'data survival test note'
```

Write:

```text
Note added:
Evidence before deployment:
Why this note matters:
```

---

# Part 2 — Make small code change locally

```bash
cd ~/projects/site5-app
vi app.py
```

Change the heading from:

```html
<h1>site5 notes</h1>
```

to:

```html
<h1>site5 notes - deployed safely</h1>
```

Check:

```bash
grep -n 'deployed safely' app.py
```

If using Git:

```bash
git status
git diff
git add app.py
git commit -m 'Update site5 heading'
```

Write:

```text
Code change:
Git evidence if used:
What this should change:
What this should not change:
```

---

# Part 3 — Back up data before deployment

```bash
sudo systemctl stop site5-app
sudo -u site5 sh -c '
mkdir -p /var/backups/site5-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site5-app/site5.db /var/backups/site5-app/site5-before-code-deploy-$timestamp.db
ls -lh /var/backups/site5-app/site5-before-code-deploy-$timestamp.db
'
sudo systemctl start site5-app
```

Write:

```text
Backup file:
Why backup comes before code deployment:
What this backup protects:
```

---

# Part 4 — Dry run code deployment

From local project:

```bash
rsync -avn --delete ./ deploy@app01:/opt/site5-app/
```

Stop and inspect output.

Write:

```text
Source path:
Destination path:
Files that would change:
Files that would be deleted:
Is /var/lib/site5-app mentioned?
Should /var/lib/site5-app ever be mentioned?
```

Do not continue if dry run mentions:

```text
/var/lib/site5-app
site5.db
```

---

# Part 5 — Deploy code

Only if dry run is safe:

```bash
rsync -av --delete ./ deploy@app01:/opt/site5-app/
sudo systemctl restart site5-app
sudo systemctl status site5-app
```

Write:

```text
Deployment command:
Restart result:
What this proves:
What this does not prove:
```

---

# Part 6 — Verify code changed and data survived

```bash
curl -s -H 'Host: site5.local' http://app01/ | grep 'deployed safely'
curl -s -H 'Host: site5.local' http://app01/ | grep 'data survival test note'
sudo tail -n 20 /var/log/nginx/site5.access.log
sudo tail -n 20 /var/log/nginx/site5.error.log
sudo journalctl -u site5-app --since '10 minutes ago'
```

Write:

```text
Code-change evidence:
Data-survival evidence:
Access-log evidence:
Error-log evidence:
App journal evidence:
Final conclusion:
```

Expected:

```text
New heading proves code deployed.
Old note proves data survived.
```

---

# Deployment checklist

```text
[ ] git status checked
[ ] git diff checked
[ ] data location identified
[ ] database backed up
[ ] rsync dry run inspected
[ ] dry run does not mention /var/lib/site5-app
[ ] deploy code
[ ] restart service
[ ] curl proves code changed
[ ] curl proves old data survived
[ ] logs checked
```

---

# Final reflection

```text
Code directory:
Data directory:
Backup file:
Dry-run evidence:
Deployment command:
Code-change evidence:
Data-survival evidence:
What --delete affected:
What --delete did not affect:
```
