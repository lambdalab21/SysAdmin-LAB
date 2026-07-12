#LAB #deployment #deployment-document
#rsync
# Site3 Deployment Runbook — Git-Tracked `rsync` + Backup + Logs

## Purpose

This document is the operational deployment runbook for `site3`.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked rsync deployment
```

Use this when deployment should be tied to a clean Git commit.

---

# Deployment model

```text
Git commit
    ↓
local public/
    ↓ rsync
app01:/var/www/site3/public/
    ↓ Nginx
site3 URL
```

Tool responsibilities:

```text
Git records the intended project version.
Backup protects the current server state.
rsync deploys the files.
curl verifies HTTP behavior.
logs show what Nginx actually saw.
```

---

# Assumptions

```text
Local project:      ~/projects/site3/
Local public dir:   ~/projects/site3/public/
Server:             app01
Deploy user:        deploy
Remote public dir:  /var/www/site3/public/
Backup dir:         /var/www/backups/site3/
URL:                http://app01/site3/  OR Host: site3.local
```

---

# Pre-deployment checklist

```text
[ ] I am in ~/projects/site3.
[ ] I know my current Git branch.
[ ] `git status` is clean.
[ ] The latest commit is the version I intend to deploy.
[ ] I will back up before rsync --delete.
[ ] I will dry-run before real rsync.
```

Commands:

```bash
cd ~/projects/site3
git status
git log --oneline --decorate -n 3
find ./public -type f -print | sort
```

---

# 1. Inspect and commit local changes

If there are changes:

```bash
git status
git diff
```

Stop and verify:

```text
What file changed?
What exact line changed?
Was this intentional?
Is there any unrelated change?
```

Commit only intended changes:

```bash
git add public/index.html public/style.css deploy.sh
git commit -m "Describe the deployment change"
```

Verify clean state:

```bash
git status
git log --oneline --decorate -n 3
```

Do not deploy unless the working tree is clean.

---

# 2. Baseline HTTP and logs

Use the correct command for your setup.

Path-based example:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://app01/site3/
```

Host-header example:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site3.local' http://app01/
```

Logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site3.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site3.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Record:

```text
HTTP status before deployment:
Access-log line:
Error-log evidence:
```

---

# 3. Create backup

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

Verify backup:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site3/site3-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```

Required evidence:

```text
[ ] Backup exists.
[ ] Backup is not zero bytes.
[ ] Archive lists public/index.html.
```

---

# 4. Dry run

Run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site3/public/
```

Record:

```text
Branch:
Latest commit:
Source path:
Destination path:
Files that would change:
Files that would be deleted:
Does rsync output match the committed change?
Destination confirmed as site3? yes/no
```

Do not deploy if the dry-run output does not match the intended Git commit.

---

# 5. Deploy

Only after Git is clean, backup is verified, and dry run is safe:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site3/public/
```

Record:

```text
Exact command:
Commit deployed:
Files changed:
Files deleted:
```

---

# 6. Verify deployment

Filesystem:

```bash
ssh deploy@app01 'grep -n "text-you-expect" /var/www/site3/public/index.html'
```

HTTP, path-based:

```bash
curl -s http://app01/site3/ | grep 'text-you-expect'
```

HTTP, host-header:

```bash
curl -s -H 'Host: site3.local' http://app01/ | grep 'text-you-expect'
```

Logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site3.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site3.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

Deployment is complete only when:

```text
[ ] Git commit is known.
[ ] Server file contains expected text.
[ ] curl shows expected content.
[ ] Access log shows the request.
[ ] Error log shows no new relevant error.
```

---

# 7. Backup-aware `deploy.sh` template

Use this as a guarded deployment script.

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

Commit script changes:

```bash
git add deploy.sh
git commit -m "Add backup-aware deployment script"
```

---

# 8. Restore test

Extract newest backup into `/tmp`:

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

Optional rollback only if needed:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site3/site3-public-*.tar.gz | head -n 1)
rm -rf /tmp/site3-rollback
mkdir -p /tmp/site3-rollback
tar -xzf "$latest" -C /tmp/site3-rollback
rsync -av --delete /tmp/site3-rollback/public/ /var/www/site3/public/
'
```

---

# Deployment record

```text
Date:
Branch:
Commit deployed:
Changed file(s):
Backup file:
Dry-run summary:
Deployment command:
Filesystem evidence:
HTTP evidence:
Access-log evidence:
Error-log evidence:
What Git proved:
What backup protected:
What rsync did:
Rollback needed? yes/no
Lesson:
```
