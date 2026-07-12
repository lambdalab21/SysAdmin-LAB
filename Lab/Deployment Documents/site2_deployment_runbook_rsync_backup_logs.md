#rsync
#LAB #deployment #deployment-document
# Site2 Deployment Runbook — `rsync` + Backup + Logs

## Purpose

This document is the operational deployment runbook for `site2`.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked deployment
```

Use this when you want the server directory to match the local `public/` directory.

---

# Deployment model

```text
local public/
    ↓ rsync
app01:/var/www/site2/public/
    ↓ Nginx
curl -H 'Host: site2.local' http://app01/
```

`rsync` is stronger than `scp`.

```text
rsync can copy changed files efficiently.
rsync --delete can remove stale server files.
rsync --delete must be dry-run first.
```

---

# Assumptions

```text
Local project:      ~/projects/site2/
Local public dir:   ~/projects/site2/public/
Server:             app01
Deploy user:        deploy
Remote public dir:  /var/www/site2/public/
Backup dir:         /var/www/backups/site2/
Host header:        site2.local
Test command:       curl -H 'Host: site2.local' http://app01/
```

---

# Pre-deployment checklist

```text
[ ] I am in ~/projects/site2.
[ ] I inspected ./public/.
[ ] I know the destination is /var/www/site2/public/.
[ ] I am not accidentally deploying to site1.
[ ] I will dry-run before using --delete.
[ ] I have a backup before real deployment.
```

Commands:

```bash
cd ~/projects/site2
pwd
find ./public -type f -print | sort
```

---

# 1. Baseline check

HTTP:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site2.local' http://app01/
```

Remote files:

```bash
ssh deploy@app01 'find /var/www/site2/public -type f -print | sort'
```

Logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site2.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site2.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Record:

```text
HTTP status before deployment:
Current remote files:
Access-log line:
Error-log evidence:
```

---

# 2. Create backup

Create backup directory:

```bash
ssh your-admin-user@app01 '
sudo mkdir -p /var/www/backups/site2
sudo chown -R deploy:deploy /var/www/backups/site2
'
```

Create backup:

```bash
ssh deploy@app01 '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf /var/www/backups/site2/site2-public-$timestamp.tar.gz -C /var/www/site2 public
ls -lh /var/www/backups/site2/site2-public-$timestamp.tar.gz
'
```

Verify backup:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site2/site2-public-*.tar.gz | head -n 1)
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

# 3. Verify local change

Example:

```bash
grep -n 'text-you-expect' public/index.html
```

Record:

```text
Changed file:
Exact local evidence:
```

---

# 4. Dry run

Run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

Stop and inspect.

Record:

```text
Source path:
Destination path:
Files that would change:
Files that would be deleted:
Unexpected deletes? yes/no
Destination confirmed as site2? yes/no
Trailing slash correct? yes/no
```

Do not deploy if the dry-run output does not make sense.

---

# 5. Deploy

Only after the dry run is safe:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site2/public/
```

Record:

```text
Exact command:
Files changed:
Files deleted:
```

---

# 6. Verify deployment

Filesystem:

```bash
ssh deploy@app01 'grep -n "text-you-expect" /var/www/site2/public/index.html'
```

HTTP:

```bash
curl -s -o /tmp/site2-after.html -w "%{http_code}\n" -H 'Host: site2.local' http://app01/
grep 'text-you-expect' /tmp/site2-after.html
```

Logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

Deployment is complete only when:

```text
[ ] Server file contains expected text.
[ ] curl returns expected status.
[ ] curl shows expected content.
[ ] Access log shows the request.
[ ] Error log shows no new relevant error.
```

---

# 7. Stale-file check

Use this occasionally to prove `--delete` behavior.

Create server-only file:

```bash
ssh deploy@app01 'echo "server-only file" > /var/www/site2/public/server-only.html'
```

Verify it exists:

```bash
curl -s -H 'Host: site2.local' http://app01/server-only.html
```

Dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

Deploy only if it clearly shows the server-only file will be deleted:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site2/public/
```

Verify:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site2.local' http://app01/server-only.html
```

Expected:

```text
404
```

---

# 8. Restore test

Extract newest backup into `/tmp`:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site2/site2-public-*.tar.gz | head -n 1)
rm -rf /tmp/site2-restore-test
mkdir -p /tmp/site2-restore-test
tar -xzf "$latest" -C /tmp/site2-restore-test
find /tmp/site2-restore-test -type f -print | sort
'
```

Optional rollback only if needed:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site2/site2-public-*.tar.gz | head -n 1)
rm -rf /tmp/site2-rollback
mkdir -p /tmp/site2-rollback
tar -xzf "$latest" -C /tmp/site2-rollback
rsync -av --delete /tmp/site2-rollback/public/ /var/www/site2/public/
'
```

---

# Deployment record

```text
Date:
Changed file(s):
Backup file:
Dry-run summary:
Deployment command:
Filesystem evidence:
HTTP evidence:
Access-log evidence:
Error-log evidence:
What --delete did:
Rollback needed? yes/no
Lesson:
```
