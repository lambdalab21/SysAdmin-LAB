#scp 
#LAB #deployment #deployment-document
# Site1 Deployment Runbook — `scp` + Backup + Logs

## Purpose

---

# Deployment model

```text
local project
    ↓ scp
app01:/var/www/site1/public/
    ↓ Nginx
http://app01/
```

`site1` is intentionally kept simple.

```text
scp copies files.
scp can overwrite files.
scp does not delete stale server files.
scp does not synchronize full directory state.
```

Do not use `rsync` for `site1`. Reserve that for `site2`.

---

# Assumptions

```text
Local project:      ~/projects/site1/
Local public dir:   ~/projects/site1/public/
Server:             app01
Deploy user:        deploy
Remote public dir:  /var/www/site1/public/
Backup dir:         /var/www/backups/site1/
URL:                http://app01/
```

Adjust paths if your machine uses different values.

---

# Pre-deployment checklist

Commands:

```bash
cd ~/projects/site1
pwd
find ./public -type f -print | sort
```

---

# 1. Baseline check

Check HTTP:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://app01/
```

Expected:

```text
200
```

Check current server files:

```bash
ssh deploy@app01 'find /var/www/site1/public -type f -print | sort'
```

Check logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.error.log'
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

# 2. Create a backup

Create backup directory if needed:

```bash
ssh your-admin-user@app01 '
sudo mkdir -p /var/www/backups/site1
sudo chown -R deploy:deploy /var/www/backups/site1
'
```

Verify write access:

```bash
ssh deploy@app01 'test -w /var/www/backups/site1 && echo deploy-can-write-backups'
```

Create a timestamped backup:

```bash
ssh deploy@app01 '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf /var/www/backups/site1/site1-public-$timestamp.tar.gz -C /var/www/site1 public
ls -lh /var/www/backups/site1/site1-public-$timestamp.tar.gz
'
```

Verify backup contents:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```

---

# 3. Verify local change before deployment

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

# 4. Deploy with `scp`

Deploy one file:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

For several files, copy each intentionally:

```bash
scp ./public/index.html ./public/style.css deploy@app01:/var/www/site1/public/
```

Avoid using broad recursive copy unless you have inspected the local directory first.

---

# 5. Verify deployment

Filesystem:

```bash
ssh deploy@app01 'grep -n "text-you-expect" /var/www/site1/public/index.html'
```

HTTP:

```bash
curl -s -o /tmp/site1-after.html -w "%{http_code}\n" http://app01/
grep 'text-you-expect' /tmp/site1-after.html
```

Logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

---

# 6. Restore test

Test extraction into `/tmp`:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
rm -rf /tmp/site1-restore-test
mkdir -p /tmp/site1-restore-test
tar -xzf "$latest" -C /tmp/site1-restore-test
find /tmp/site1-restore-test -type f -print | sort
'
```

Optional rollback only if needed:

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
rm -rf /tmp/site1-rollback
mkdir -p /tmp/site1-rollback
tar -xzf "$latest" -C /tmp/site1-rollback
cp -a /tmp/site1-rollback/public/. /var/www/site1/public/
'
```

Verify after rollback:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://app01/
```

---

# Deployment record

```text
Date:
Changed file(s):
Backup file:
Deployment command:
Filesystem evidence:
HTTP evidence:
Access-log evidence:
Error-log evidence:
What scp did:
What scp did not do:
Rollback needed? yes/no
Lesson:
```
