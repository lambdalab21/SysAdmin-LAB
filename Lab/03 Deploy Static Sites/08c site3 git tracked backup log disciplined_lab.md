# 10 — `site3` Git-Tracked Deployment: Backup + Log Checks

## Role of this lab

`site3` is the **Git-tracked deployment**.

```text
site1 = scp copy deployment
site2 = rsync synchronized deployment
site3 = Git-tracked rsync deployment
```

Git does not deploy files.

Git records the project version.

`rsync` deploys the files.

`curl` and logs prove what the server served.

---

# Goal

Build this deployment habit:

```text
inspect Git changes
→ commit intentionally
→ back up current server state
→ rsync dry run
→ deploy
→ verify filesystem
→ verify HTTP
→ verify logs
→ write deployment note
```

---

# Rules

1. Do not deploy uncommitted work.
2. Do not deploy before `git diff`.
3. Do not run real `rsync` before dry run.
4. Back up before deploying with `--delete`.
5. Do not call deployment successful until `curl` and logs agree.
6. Do not edit deployed files directly on `app01`.

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

Write:

```text
Current branch:

Latest commit:

Working tree clean? yes/no

If not clean, what changed?
```

Stop.

Do not deploy unless you can explain what version you are about to send.

Green check:

```text
[ ] I know my branch.
[ ] I know my latest commit.
[ ] I know whether the working tree is clean.
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
What file changed?

What exact line changed?

Was this intentional?

Is there any unrelated change?
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

Green check:

```text
[ ] I inspected git diff.
[ ] I committed only the intended change.
[ ] Working tree is clean.
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
HTTP status before deployment:

Access-log line:

Error-log evidence:

What this proves:

What this does not prove:
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

Write:

```text
Backup file:

Backup contents:

What this proves:

What this does not prove:
```

Green check:

```text
[ ] Backup exists.
[ ] Backup lists public/index.html.
[ ] Backup was made before deployment.
```

---

# Part 5 — Dry run and deploy

Dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site3/public/
```

Stop and write:

```text
Latest commit being deployed:

Source path:

Destination path:

Files that would change:

Files that would be deleted:

Does rsync output match git diff?

Is destination definitely site3?
```

Only if correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site3/public/
```

Write:

```text
Deployment command:

What changed:

What this does not prove:
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

Write:

```text
Git evidence:

Filesystem evidence:

HTTP evidence:

Access-log evidence:

Error-log evidence:

Final conclusion:
```

Expected distinction:

```text
Git proves what version I intended to deploy.
Filesystem proves files reached app01.
curl proves Nginx served the content.
Logs prove Nginx received the request and whether it had errors.
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
Restore-test evidence:

What this proves:

What this does not prove:
```

---

# Deployment journal

Complete after every `site3` deployment:

```text
Project:

Branch:

Commit deployed:

Backup file:

Rsync dry-run summary:

Deployment command:

Filesystem evidence:

HTTP evidence:

Access-log evidence:

Error-log evidence:

What Git proved:

What backup protects:

What rsync proved:

What curl proved:

What logs proved:

One mistake I avoided:

One habit I improved:
```

Completion checkpoint:

```text
[ ] site3 uses Git before deployment.
[ ] Git status was clean before deployment.
[ ] Backup was made before rsync --delete.
[ ] rsync dry run was inspected.
[ ] curl verified served content.
[ ] logs were checked.
[ ] I can explain Git vs backup vs rsync vs Nginx.
```
