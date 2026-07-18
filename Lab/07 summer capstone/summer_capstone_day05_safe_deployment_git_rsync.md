# Day 5 — Safe Deployment with Git and rsync

## Expected time

```text
Target: 120–180 minutes
Minimum: 90 minutes
Deep version: 3 hours with one intentional rollback/restore discussion
```

## Goal

Deploy code without destroying data.

Key idea:

```text
Git protects code history.
Backups protect live data.
rsync deploys files.
curl and logs prove what the server served.
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


# Part 1 — Git review

In local project:

```bash
cd ~/projects/site6-crud
git status
git log --oneline --decorate -n 5
```

Write:

```text
Current branch:
Latest commit:
Working tree clean?
What Git proves:
What Git does not prove:
```

# Part 2 — Make a tiny code change

Change a visible heading or footer.

Example:

```text
site6 CRUD capstone
```

Check:

```bash
git diff
```

Commit:

```bash
git add .
git commit -m "Update capstone heading"
```

Write:

```text
Change:
Diff evidence:
Commit:
What should change after deployment:
What should not change:
```

# Part 3 — Back up data before deployment

On app01:

```bash
sudo systemctl stop site6-app

sudo -u site6 sh -c '
mkdir -p /var/backups/site6-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site6-app/site6.db /var/backups/site6-app/site6-before-deploy-$timestamp.db
ls -lh /var/backups/site6-app/site6-before-deploy-$timestamp.db
'

sudo systemctl start site6-app
```

Write:

```text
Backup file:
Why backup before deployment:
```

# Part 4 — rsync dry run

From local project:

```bash
rsync -avn --delete   --exclude '.git'   --exclude '.venv'   --exclude 'data'   ./ deploy@app01:/opt/site6-app/
```

Stop and inspect.

Write:

```text
Source:
Destination:
Files that would change:
Files that would be deleted:
Does output mention /var/lib/site6-app?
Does output mention site6.db?
Is dry run safe?
```

# Part 5 — Deploy code

Only if dry run is safe:

```bash
rsync -av --delete   --exclude '.git'   --exclude '.venv'   --exclude 'data'   ./ deploy@app01:/opt/site6-app/

sudo systemctl restart site6-app
sudo systemctl status site6-app
```

Verify code change:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'capstone'
```

Verify old data still exists:

```bash
curl -s -H 'Host: site6.local' http://app01/ | grep 'backup proof task'
```

Check logs:

```bash
sudo tail -n 20 /var/log/nginx/site6.access.log
sudo tail -n 20 /var/log/nginx/site6.error.log
sudo journalctl -u site6-app --since "10 minutes ago"
```

# Deliverable

Create or improve:

```text
DEPLOYMENT.md
```

Required sections:

```text
# Deployment

## Pre-deployment checks
## Git status and diff
## Database backup
## rsync dry run
## Real deployment
## Restart service
## HTTP verification
## Log verification
## Data survival check
## Rollback or restore notes
```

# Completion checklist

```text
[ ] I checked Git status.
[ ] I inspected git diff.
[ ] I committed intentionally.
[ ] I backed up data.
[ ] I inspected rsync dry run.
[ ] I deployed code.
[ ] I proved code changed.
[ ] I proved data survived.
[ ] I updated DEPLOYMENT.md.
```
