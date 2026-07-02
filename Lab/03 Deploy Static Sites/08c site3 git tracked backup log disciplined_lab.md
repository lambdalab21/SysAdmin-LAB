# 10 — `site3` Git-Tracked Deployment: Backup + Log Checks

## Role of this lab

`site3` is the **Git-tracked deployment**.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked rsync deployment
```

Git does not deploy files.

---

# Current setup

```text
Local project:        ~/projects/site3/
Local public dir:     ~/projects/site3/public/
Server:               app01
Deploy user:          deploy
Remote public dir:    /var/www/site3/public/
Backup dir:           /var/www/backups/site3/
URL or Host header:   your site3 test URL
```

Write actual values:

```text
Local project:

Remote public directory:

Backup directory:

Test URL or curl command:
```

---

# Part 1 — Git status before touching deployment

From the development machine:

```bash
cd ~/projects/site3
git status
git log --oneline --decorate -n 3
```

---

# Part 2 — Make one local change and inspect it

Edit:

```bash
vi public/index.html
```

Add:

```html
<p>site3 Git backup/log practice.</p>
```

Inspect:

```bash
git status
git diff
```

Stop and answer:

```text
What file changed? index.html

What exact line changed? Line 27; added <p>site3 Git backup/log practice.</p>

Was this intentional? Yes 

Is there any unrelated change? No
```

Commit:

```bash
git add public/index.html
git commit -m "Update site3 deployment practice text"
```

Verify:

```bash
git status
git log --oneline --decorate -n 3
```


---

# Part 3 — Baseline HTTP and logs

Before deployment, check what the server currently serves.

Use the correct command for your Nginx setup, for example:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://app01/site3/
```

or:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site3.local' http://app01/
```

Check logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site3.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site3.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Write:

```text
HTTP status before deployment: 200. 

Access-log line: `app01 - - [...] "GET /site3/HTTP/1.1" 200 ...`

Error-log evidence: No recent error. 

What this proves: Current live content before deployment. 

What this does not prove: That the new change is live. 
```

---

# Part 4 — Back up current server state

Create backup directory:

```bash
ssh your-admin-user@app01 '
sudo mkdir -p /var/www/backups/site3
sudo chown -R deploy:deploy /var/www/backups/site3
'
```

Create backup:

```bash
ssh deploy@app01 '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf /var/www/backups/site3/site3-public-$timestamp.tar.gz -C /var/www/site3 public
ls -lh /var/www/backups/site3/site3-public-$timestamp.tar.gz
'
```

Verify:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site3/site3-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```


# Part 5 — Dry run and deploy

Dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site3/public/
```

Stop and write:

```text
Latest commit being deployed: "Update site3 deployment practice text"

Source path: `./public/`

Destination path: deploy@app01:/var/www/site3/public/

Files that would change: public/idnex.html

Does rsync output match git diff? Yes. 
```

Only if correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site3/public/
```

---

# Part 6 — Verify after deployment

## Filesystem

```bash
ssh deploy@app01 'grep -n "Git backup/log practice" /var/www/site3/public/index.html'
```

## HTTP

Use your correct URL, for example:

```bash
curl -s http://app01/site3/ | grep 'Git backup/log practice'
```

or:

```bash
curl -s -H 'Host: site3.local' http://app01/ | grep 'Git backup/log practice'
```

## Logs

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site3.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site3.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

--- 

# Part 7 — Update `deploy.sh` to include backup and log prompts

Your `deploy.sh` should not hide thinking. It should enforce it.

Minimum behavior:

```text
show git status
refuse dirty working tree
show latest commit
create backup
verify backup can be listed
show rsync dry run
ask for confirmation
deploy
run curl
tell user to inspect logs
```

Use this script as a starting point:

```bash
#!/usr/bin/env bash
set -euo pipefail

LOCAL_DIR="./public/"
REMOTE_USER="deploy"
REMOTE_HOST="app01"
REMOTE_DIR="/var/www/site3/public/"
REMOTE_SITE_ROOT="/var/www/site3"
BACKUP_DIR="/var/www/backups/site3"
URL="http://app01/site3/"

if [[ -n "$(git status --porcelain)" ]]; then
  echo "ERROR: working tree is not clean."
  git status --short
  exit 1
fi

echo "Latest commit:"
git log --oneline -n 1

echo
echo "Creating backup..."
ssh "$REMOTE_USER@$REMOTE_HOST" "
set -e
mkdir -p '$BACKUP_DIR'
timestamp=\$(date +%Y%m%d-%H%M%S)
tar -czf '$BACKUP_DIR/site3-public-'\$timestamp'.tar.gz' -C '$REMOTE_SITE_ROOT' public
latest=\$(ls -t '$BACKUP_DIR'/site3-public-*.tar.gz | head -n 1)
echo \"Backup: \$latest\"
tar -tzf \"\$latest\" | head
"

echo
echo "rsync dry run:"
rsync -avn --delete "$LOCAL_DIR" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
read -r -p "Deploy this committed version? [y/N] " answer
if [[ "$answer" != "y" && "$answer" != "Y" ]]; then
  echo "Cancelled."
  exit 0
fi

rsync -av --delete "$LOCAL_DIR" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
echo "HTTP check:"
curl -fsS "$URL" >/dev/null
echo "curl OK"

echo
echo "Now inspect Nginx access and error logs."
```

Commit the script:

```bash
git add deploy.sh
git commit -m "Add backup-aware deployment script"
```

Stop and explain:

```text
Why does this script refuse dirty work?

Why does it make a backup before rsync?

Why does it still show dry run?

What does curl prove?

What do logs prove?
```

---

# Part 8 — Restore test

Test extraction into `/tmp`:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site3/site3-public-*.tar.gz | head -n 1)
rm -rf /tmp/site3-restore-test
mkdir -p /tmp/site3-restore-test
tar -xzf "$latest" -C /tmp/site3-restore-test
find /tmp/site3-restore-test -type f -print | sort
'
```

Write:

```text
Restore-test evidence: Files from the backup successfully extracted to /tmp. 

What this proves: Backups are valid and restorable. 

What this does not prove: That a full production restore would succeed without issue. 
```

---

# Deployment journal

Complete after every `site3` deployment:

```text
Project: site3

Branch: main 

Commit deployed: Update site3 deployment practice text

Backup file: site3-public-YYYYMMDD-HHMMSS.tar.gz

Rsync dry-run summary: Only index.html updated

Deployment command: rsync -av --delete

Filesystem evidence: New text present

HTTP evidence: curl shows new content. 

Access-log evidence: 200 OK entries. 

Error-log evidence: Clean

What Git proved: Intended Version

What backup protects: Rollback Capability. 

What rsync proved: Files transferred correctly. 

What curl proved: Content is live. 

What logs proved: Request handling status. 

One mistake I avoided: Deploying dirty tree. 

One habit I improved: Always checking logs after deployment. 
```