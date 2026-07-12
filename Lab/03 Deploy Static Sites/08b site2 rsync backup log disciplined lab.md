#LAB #deployment
#ssh
# 09 — `site2` Deployment with `rsync`: Backup + Log Checks

## Role of this lab

`site2` is the **rsync deployment**.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked deployment
```

This lab adds a backup and log check around `rsync`.

The goal:

```text
backup current server state
→ inspect rsync dry run
→ deploy with rsync
→ verify filesystem
→ verify HTTP
→ verify logs
→ prove stale files are handled
```

---

# Rules

1. Use `rsync` for `site2`.
2. Keep `site1` separate.
3. Always dry-run before `--delete`.
4. Back up before real deployment with `--delete`.
5. Do not edit files directly on `app01`.
6. Do not declare success without evidence.
7. Do not check boxes without exact evidence.

---

# Current setup

```text
Local project:        ~/projects/site2/
Local public dir:     ~/projects/site2/public/
Server:               app01
Deploy user:          deploy
Remote public dir:    /var/www/site2/public/
Backup dir:           /var/www/backups/site2/
Host header:          site2.local
Test command:         curl -H 'Host: site2.local' http://app01/
```

Write your actual values:

```text
My local project directory:

My remote public directory:

My backup directory:

My test command:
```

---

# Part 1 — Stop: why backup matters more with `rsync --delete`

Answer before typing:

```text
What does rsync do that scp does not?

What does --delete do?

What could go wrong if the destination path is wrong?

What directory am I backing up?
```

Expected:

```text
rsync can make the server directory match the local directory.
--delete removes remote files missing locally.
A wrong destination path can delete the wrong files.
Therefore I back up /var/www/site2/public/ before --delete.
```

Green check:

```text
[ ] I know site2 uses rsync.
[ ] I know why --delete needs a backup.
[ ] I know the exact destination path.
```

---

# Part 2 — Baseline HTTP and logs

Make a baseline request:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site2.local' http://app01/
```

Check remote files:

```bash
ssh deploy@app01 'find /var/www/site2/public -type f -print | sort'
```

Check logs:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site2.access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site2.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Write:

```text
HTTP status before deployment:

Current remote files:

Access-log line:

Error-log evidence:

What this proves:

What this does not prove:
```

---

# Part 3 — Create and verify backup

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

Verify:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site2/site2-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```

Green check:

```text
[ ] Backup exists.
[ ] Backup is not zero bytes.
[ ] tar -tzf lists public/index.html.
[ ] I know how to find the newest backup.
```

---

# Part 4 — Make a local change

```bash
cd ~/projects/site2
vi public/index.html
```

Add:

```html
<p>site2 rsync backup/log practice.</p>
```

Verify locally:

```bash
grep -n 'rsync backup/log practice' public/index.html
```

Write:

```text
Changed file:

Exact local evidence:
```

---

# Part 5 — Dry run before deployment

Run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

Stop.

Do not deploy yet.

Write:

```text
Source path:

Destination path:

Files that would change:

Files that would be deleted:

Is this site2, not site1?

Is the trailing slash correct?

Why is this safe?
```

Green check:

```text
[ ] I inspected the dry-run output.
[ ] Destination is /var/www/site2/public/.
[ ] No unexpected deletion appears.
[ ] I have not run real rsync yet.
```

---

# Part 6 — Deploy with `rsync`

Only if the dry run makes sense:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site2/public/
```

Write:

```text
Exact command:

What changed:

What did --delete do or not do:

What this does not prove:
```

---

# Part 7 — Verify after deployment

## Filesystem

```bash
ssh deploy@app01 'grep -n "rsync backup/log practice" /var/www/site2/public/index.html'
```

## HTTP

```bash
curl -s -o /tmp/site2-after.html -w "%{http_code}\n" -H 'Host: site2.local' http://app01/
grep 'rsync backup/log practice' /tmp/site2-after.html
```

## Logs

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.error.log'
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
[ ] curl shows the new content.
[ ] Access log shows my request.
[ ] Error log shows no new relevant error.
[ ] I did not declare success from rsync alone.
```

---

# Part 8 — Stale file check

Create a file directly on the server:

```bash
ssh deploy@app01 'echo "server-only file" > /var/www/site2/public/server-only.html'
```

Verify:

```bash
curl -s -H 'Host: site2.local' http://app01/server-only.html
```

Dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

Stop and write:

```text
What would --delete remove?

Is that expected?

Which backup protects me?
```

Deploy only if correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site2/public/
```

Verify stale file is gone:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -H 'Host: site2.local' http://app01/server-only.html
```

Expected:

```text
404
```

Check log:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site2.access.log'
```

Write:

```text
Stale file result:

Access-log evidence:

Lesson:
```

---

# Part 9 — Restore test

Test extraction into `/tmp`:

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

Write:

```text
Restore test evidence:

What this proves:

What this does not prove:
```

Optional real rollback if needed:

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

Use real rollback only intentionally.

---

# Final reflection

```text
Task:

Backup file:

rsync dry-run evidence:

Deployment command:

Filesystem evidence:

HTTP evidence:

Log evidence:

What --delete did:

What backup protects:

One mistake I avoided:

One habit I improved:
```

Completion checkpoint:

```text
[ ] I can explain why site2 uses rsync.
[ ] I can explain why backup matters before --delete.
[ ] I can explain dry run.
[ ] I can explain stale files.
[ ] I can quote an access-log line.
[ ] I can restore into /tmp.
```
