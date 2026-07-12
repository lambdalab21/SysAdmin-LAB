#LAB #deployment
#ssh
# 08 — `site1` Deployment with `scp`: Backup + Log Checks

## Role of this lab

`site1` stays as the **scp deployment baseline**.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked deployment
```

This lab adds a disciplined pre-deployment backup and a short log check.

The goal is not to make deployment complicated.

The goal is to build this habit:

```text
know what will change
→ back up current server state
→ verify backup
→ deploy with scp
→ verify filesystem
→ verify HTTP
→ verify logs
→ reflect
```

---

# Rules

1. Edit files locally.
2. Deploy `site1` only with `scp`.
3. Do not use `rsync` in this lab.
4. Do not edit `/var/www/site1/public/` manually.
5. Do not deploy as `root`.
6. Do not call deployment successful until filesystem, HTTP, and logs agree.
7. Do not check a box unless you can point to evidence.

---

# Current setup

```text
Local project:        ~/projects/site1/
Local public dir:     ~/projects/site1/public/
Server:               app01
Deploy user:          deploy
Remote public dir:    /var/www/site1/public/
Backup dir:           /var/www/backups/site1/
URL:                  http://app01/
```

Write your actual values:

```text
My local project directory:

My local public directory:

My server hostname or IP:

My remote public directory:

My backup directory:

My test URL:
```

---

# Part 1 — Stop: what can this deployment change?

Before typing commands, answer:

```text
What exact file or directory will I change on app01?

Will scp overwrite a file?

Will scp delete server files?

What is the source of truth?

What is only a deployed copy?
```

Expected:

```text
scp can overwrite files that I copy.
scp does not remove stale server files.
The local project is the source of truth.
The server directory is a deployed copy.
```

Green check:

```text
[ ] I know the exact remote path.
[ ] I know scp copies/overwrites but does not synchronize.
[ ] I know what I am protecting with a backup.
```

---

# Part 2 — Baseline check before backup

From the development machine:

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

Check recent logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.error.log'
```

If the site-specific logs do not exist, use:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Write:

```text
HTTP status before deployment:

Current server files:

Access-log line showing my request:

Error-log evidence:

What this proves:

What this does not prove:
```

Stop here.

Do not write:

```text
logs are fine
```

Quote the exact log line or say exactly what you could not find.

Green check:

```text
[ ] I know the site worked before deployment.
[ ] I found a matching access-log line or explained why I could not.
[ ] I checked the error log.
```

---

# Part 3 — Create and verify a backup

Create the backup directory if needed:

```bash
ssh your-admin-user@app01 '
sudo mkdir -p /var/www/backups/site1
sudo chown -R deploy:deploy /var/www/backups/site1
'
```

Verify `deploy` can write:

```bash
ssh deploy@app01 'test -w /var/www/backups/site1 && echo deploy-can-write-backups'
```

Create a timestamped archive of the current server copy:

```bash
ssh deploy@app01 '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf /var/www/backups/site1/site1-public-$timestamp.tar.gz -C /var/www/site1 public
ls -lh /var/www/backups/site1/site1-public-$timestamp.tar.gz
'
```

## Feynman check

Explain:

```text
tar is putting the current server public directory into one compressed file.
The timestamp prevents one backup from overwriting another.
```

Verify the archive contents:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```

Write:

```text
Backup file:

Backup size:

Archive contents include:

What this proves:

What this does not prove:
```

Green check:

```text
[ ] Backup file exists.
[ ] Backup is not zero bytes.
[ ] tar -tzf lists the archive.
[ ] Archive contains public/index.html.
```

---

# Part 4 — Make one local change

On the development machine:

```bash
cd ~/projects/site1
vi public/index.html
```

Add one visible line:

```html
<p>site1 scp backup/log practice.</p>
```

Verify locally:

```bash
grep -n 'scp backup/log practice' public/index.html
```

Write:

```text
Changed file:

Exact change:

Local evidence:
```

Stop and ask:

```text
Am I changing one file only?
Am I still inside the local project?
Am I about to deploy from local to server, not the reverse?
```

---

# Part 5 — Deploy with `scp`

Predict before running:

```text
Source path:

Destination path:

Will this delete stale files?

Which backup protects me?
```

Deploy one file:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

Write:

```text
Exact command:

What this proves:

What this does not prove:
```

Expected:

```text
scp success proves the file transfer completed.
It does not prove Nginx served the new content.
It does not prove other files are synchronized.
It does not remove stale files.
```

---

# Part 6 — Verify after deployment

## Filesystem evidence

```bash
ssh deploy@app01 'grep -n "scp backup/log practice" /var/www/site1/public/index.html'
```

## HTTP evidence

```bash
curl -s -o /tmp/site1-after.html -w "%{http_code}\n" http://app01/
grep 'scp backup/log practice' /tmp/site1-after.html
```

## Log evidence

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

Write:

```text
Filesystem evidence:

HTTP status:

HTTP content evidence:

Access-log line:

Error-log evidence:

Final conclusion:
```

Green check:

```text
[ ] Server file contains the new text.
[ ] curl returns expected HTTP status.
[ ] curl shows the new text.
[ ] Access log shows my request.
[ ] Error log shows no new relevant error.
[ ] I did not declare success from scp alone.
```

---

# Part 7 — Restore test, not real rollback

Do not overwrite the running site yet. First test extraction into `/tmp`.

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

Write:

```text
Restore-test directory:

Files restored:

Does public/index.html appear?

What this proves:

What this does not prove:
```

Optional real rollback only if needed:

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

Use `cp` here because `site1` is the `scp`/copy baseline. Save `rsync --delete` for `site2`.

---

# Part 8 — Final reflection

```text
Task:

Backup file:

Deployment command:

Strongest filesystem evidence:

Strongest HTTP evidence:

Strongest log evidence:

What scp did:

What scp did not do:

What the backup protects:

What the backup does not protect:

One mistake I avoided:

One habit I improved:
```

Completion checkpoint:

```text
[ ] I can explain why site1 uses scp.
[ ] I can explain why backup comes before deployment.
[ ] I can explain why logs are relevant even with no error.
[ ] I can explain why scp does not remove stale files.
[ ] I can restore the archive into /tmp.
```
