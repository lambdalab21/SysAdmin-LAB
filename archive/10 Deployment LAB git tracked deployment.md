# Phase 3 Lab: Deploy `site03` with Git + `rsync`

## Purpose

You already know basic Git commands:

```text
git init
git status
git add
git commit
git branch
git switch
git stash
```

This lab is not mainly about learning new Git commands.

It is about using Git to build a disciplined deployment habit:

```text
edit locally
→ inspect exactly what changed
→ commit intentionally
→ dry-run deployment
→ deploy
→ verify with curl and logs
→ write a short deployment note
```

## Big idea

`site01` taught basic copying with `scp`.

`site02` taught directory synchronization with `rsync`.

`site03` adds Git as the safety layer before deployment.

```text
site01 = copied with scp
site02 = synchronized with rsync
site03 = tracked with Git, deployed with rsync
```

## Feynman explanation

Explain this to a 10-year-old:

```text
Git is like a notebook that records each clean version of your project.
rsync is the delivery truck that sends the current website files to the server.
Nginx is the store clerk that shows the website files to visitors.
```

Do not confuse the jobs:

| Tool | Job |
|---|---|
| Git | Records project history |
| rsync | Copies/synchronizes files to the server |
| Nginx | Serves files to browsers |
| curl | Tests what the server returns |
| logs | Show what actually happened |

---

# Rules for this lab

1. Do not edit files directly on `app01`.
2. Do not deploy from an uncommitted mess.
3. Do not run real `rsync` before dry run.
4. Do not use `--delete` unless you read the dry-run output.
5. Do not call deployment successful until `curl` and logs agree.
6. Do not write “it worked” without evidence.

## Before each important command

Write:

```text
Question:
Command:
Expected evidence:
Risk if I am wrong:
```

## After each important command

Write:

```text
Exact output or exact observation:
What this proves:
What this does not prove:
Next narrow question:
```

If you cannot fill these in, stop. You are typing instead of thinking.

---

# Starting assumptions

This lab assumes:

- Nginx is already installed on `app01`.
- The firewall already allows HTTP.
- SSH works.
- The user `deploy` can write under `/var/www` where needed.
- You already deployed `site01` and `site02` in earlier labs.

For this lab, use:

```text
Local project:  ~/projects/site03
Remote target:  /var/www/site03/public/
URL:            http://app01/site03/  OR  http://site03.local/
```

Your Nginx setup may use either a separate virtual host or a path-based location. Use whichever your instructor configured. If unsure, first verify where Nginx expects `site03` files.

---

# Part 1: Prepare the local project

On the development machine:

```bash
mkdir -p ~/projects/site03/public
cd ~/projects/site03
```

Create `public/index.html`:

```bash
vi public/index.html
```

Use:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>site03</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>site03</h1>
  <p>This site is tracked with Git and deployed with rsync.</p>
</body>
</html>
```

Create `public/style.css`:

```bash
vi public/style.css
```

Use:

```css
body {
  font-family: sans-serif;
  max-width: 720px;
  margin: 3rem auto;
  line-height: 1.5;
}
```

Inspect:

```bash
find ./public -type f -print
```

## Stop and think

Do not continue until you can answer:

```text
Which files are public website files?
Which directory should be deployed?
Which files should not be deployed?
```

Good answer:

```text
Only ./public/ should be deployed. Git files, notes, scripts, secrets, and private files should not be served by Nginx.
```

✅ Green check:

```text
[ ] I can explain why public/ is the deployment source.
```

---

# Part 2: Initialize Git and make the first commit

Run:

```bash
git init
git status
```

Before adding files, pause.

## Stop and think

Git is asking:

```text
What should be remembered in this version?
```

Run:

```bash
git add public/index.html public/style.css
git status
```

Commit:

```bash
git commit -m "Create initial site03 static page"
```

Inspect:

```bash
git log --oneline --decorate -n 3
```

## Questions

1. What changed between `git init` and the first commit?
2. Which files are now tracked?
3. What does `git status` say after the commit?
4. Why is a clean working tree useful before deployment?

## Feynman checkpoint

Explain Git commit like this:

```text
A commit is a labeled snapshot of the project that I can return to later.
```

Then explain it in your own words.

✅ Green check:

```text
[ ] git status is clean.
[ ] I know which files are tracked.
[ ] I can explain what the first commit contains.
```

---

# Part 3: Confirm or create the remote target

SSH into `app01` as an admin user if directory setup still needs root privileges:

```bash
ssh your-admin-user@app01
```

Check the directory:

```bash
ls -ld /var/www/site03
ls -ld /var/www/site03/public
```

If it does not exist:

```bash
sudo mkdir -p /var/www/site03/public
sudo chown -R deploy:nginx /var/www/site03
sudo chmod -R 2755 /var/www/site03
```

Inspect:

```bash
ls -ld /var/www/site03 /var/www/site03/public
exit
```

From the development machine, test deploy-user write access:

```bash
ssh deploy@app01 'test -w /var/www/site03/public && echo deploy-can-write'
```

## Stop and think

Do not continue unless you can explain:

```text
Who writes deployed files?
Who reads deployed files?
Why should normal deployment not require sudo?
```

Good answer:

```text
deploy writes files. nginx reads files. Normal deployment should not require sudo because writing the website files is not the same as changing system configuration.
```

✅ Green check:

```text
[ ] deploy can write to /var/www/site03/public.
[ ] I did not deploy as root.
```

---

# Part 4: First rsync dry run

From the development machine:

```bash
cd ~/projects/site03
```

Run dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site03/public/
```

## Stop here

Do not remove `-n` yet.

Write:

```text
Question:
What would rsync change on the server?

Command:
rsync -avn --delete ./public/ deploy@app01:/var/www/site03/public/

Expected evidence:
I should see index.html and style.css listed as files that would be transferred.

Risk if I am wrong:
A wrong destination path with --delete could remove files from the wrong location.
```

Now read the dry-run output line by line.

## Questions

1. Which files would be copied?
2. Would anything be deleted?
3. Is the destination path exactly `/var/www/site03/public/`?
4. Does the trailing slash after `./public/` mean contents or directory?

✅ Green check:

```text
[ ] I read the dry-run output.
[ ] The destination path is correct.
[ ] I understand what --delete would do.
[ ] I have not changed the server yet.
```

---

# Part 5: First real deployment

Only after the dry run makes sense:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site03/public/
```

Verify remote files:

```bash
ssh deploy@app01 'find /var/www/site03/public -type f -print'
```

Test with `curl`.

Use the URL that matches your Nginx setup, for example:

```bash
curl -v http://app01/site03/
```

or:

```bash
curl -v http://site03.local/
```

Inspect logs on `app01`:

```bash
ssh your-admin-user@app01
sudo tail -n 20 /var/log/nginx/site1.access.log 2>/dev/null || true
sudo tail -n 20 /var/log/nginx/access.log 2>/dev/null || true
sudo tail -n 20 /var/log/nginx/error.log 2>/dev/null || true
exit
```

If your site has a dedicated log file for `site03`, inspect that instead.

## Stop and think

Do not write “it worked.” Write evidence.

```text
Remote file evidence:

HTTP evidence:

Log evidence:
```

✅ Green check:

```text
[ ] Files exist on app01.
[ ] curl returns the expected page.
[ ] Logs show the request or show no error.
```

---

# Part 6: Make a change, inspect Git before deploying

Edit locally:

```bash
vi public/index.html
```

Change the paragraph to:

```html
<p>This version was changed locally, reviewed with Git, and deployed intentionally.</p>
```

Now pause.

Run:

```bash
git status
git diff
```

## Stop here

This is one of the most important moments in the lab.

Before committing or deploying, answer:

```text
What file changed?
What exact line changed?
Was this change intentional?
Is there any unrelated change?
```

If there is an unrelated change, do not deploy yet.

Commit:

```bash
git add public/index.html
git commit -m "Update site03 homepage text"
```

Check:

```bash
git status
git log --oneline --decorate -n 3
```

✅ Green check:

```text
[ ] I inspected git diff before committing.
[ ] I committed only the intended change.
[ ] git status is clean before deployment.
```

---

# Part 7: Deploy the committed change

Dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site03/public/
```

Answer before real deployment:

```text
Which file will rsync transfer?
Does the output match my Git change?
Is anything unexpected being deleted?
```

Deploy:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site03/public/
```

Verify:

```bash
curl http://app01/site03/
```

or:

```bash
curl http://site03.local/
```

## Important comparison

Git proves:

```text
what changed in the project
```

rsync proves:

```text
what changed on the server
```

curl proves:

```text
what the web server actually returns
```

Logs prove:

```text
what the server actually saw
```

✅ Green check:

```text
[ ] Git change matches rsync transfer.
[ ] curl shows the new page.
[ ] Logs do not show an error.
```

---

# Part 8: Create `deploy.sh` with Git safety checks

Create:

```bash
vi deploy.sh
```

Use:

```bash
#!/usr/bin/env bash
set -euo pipefail

LOCAL_DIR="./public/"
REMOTE_USER="deploy"
REMOTE_HOST="app01"
REMOTE_DIR="/var/www/site03/public/"
URL="http://app01/site03/"

if ! git rev-parse --is-inside-work-tree >/dev/null 2>&1; then
  echo "ERROR: This directory is not inside a Git repository."
  exit 1
fi

echo "== Git status =="
git status --short

if [[ -n "$(git status --porcelain)" ]]; then
  echo
  echo "ERROR: Working tree is not clean."
  echo "Commit, stash, or discard changes before deploying."
  exit 1
fi

echo
echo "== Latest commit =="
git log --oneline -n 1

echo
echo "== rsync dry run =="
rsync -avn --delete "$LOCAL_DIR" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
read -r -p "Deploy this committed version? [y/N] " answer

if [[ "$answer" != "y" && "$answer" != "Y" ]]; then
  echo "Deployment cancelled."
  exit 0
fi

echo
echo "== Deploying =="
rsync -av --delete "$LOCAL_DIR" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
echo "== Verifying HTTP response =="
curl -fsS "$URL" >/dev/null

echo "Deployment complete and HTTP check passed."
echo "Now inspect Nginx logs if needed."
```

Make executable:

```bash
chmod +x deploy.sh
```

Commit the script:

```bash
git add deploy.sh
git commit -m "Add guarded deployment script"
```

## Stop and think

Explain:

```text
Why does the script refuse to deploy with uncommitted changes?
Why does it show the latest commit?
Why does it dry-run before deploying?
Why does it use curl after deploying?
```

✅ Green check:

```text
[ ] deploy.sh is tracked by Git.
[ ] deploy.sh refuses dirty deployments.
[ ] deploy.sh dry-runs before real deployment.
[ ] deploy.sh verifies HTTP after deployment.
```

---

# Part 9: Test the safety gate

Make an uncommitted change:

```bash
echo '<p>Uncommitted test</p>' >> public/index.html
```

Run:

```bash
./deploy.sh
```

Expected:

```text
The script should refuse to deploy.
```

Now inspect:

```bash
git status
git diff
```

Undo the test change:

```bash
git restore public/index.html
```

Check:

```bash
git status
```

## Questions

1. Did the script protect you from deploying uncommitted work?
2. What evidence shows that the working tree was dirty?
3. What command restored the file?
4. Why is this safer than manually typing rsync?

✅ Green check:

```text
[ ] I saw the deployment script refuse dirty work.
[ ] I inspected the dirty change with git diff.
[ ] I restored the file cleanly.
```

---

# Part 10: Branch experiment

Create a branch:

```bash
git switch -c experiment-banner
```

Edit `public/index.html` and add:

```html
<p><strong>Experimental banner</strong></p>
```

Inspect:

```bash
git diff
```

Commit:

```bash
git add public/index.html
git commit -m "Add experimental banner"
```

Now pause.

## Stop and think

A branch is not automatically a deployment.

Answer:

```text
Which branch am I on?
Is this experiment ready to deploy?
Should experiments be deployed to production-like site03?
```

Check branch:

```bash
git branch --show-current
```

For this lab, deploy the branch intentionally once:

```bash
./deploy.sh
```

Verify with curl.

Then return to main:

```bash
git switch main
```

If your default branch is `master`, use:

```bash
git switch master
```

Deploy main again:

```bash
./deploy.sh
```

Verify that the experimental banner disappeared from the served page.

## Questions

1. What changed when switching branches locally?
2. Did the server change merely because you switched branches?
3. When did the server actually change?
4. Why is branch switching different from deployment?

✅ Green check:

```text
[ ] I understand that Git branch changes are local until deployed.
[ ] I verified the server content after each deployment.
```

---

# Part 11: Tiny rollback exercise

Find recent commits:

```bash
git log --oneline -n 5
```

Use Git to return to a previous version temporarily.

Option A: use branch switching if the previous version is on `main`.

Option B: use a temporary rollback branch:

```bash
git switch -c rollback-test HEAD~1
```

Deploy:

```bash
./deploy.sh
```

Verify with curl.

Return to main:

```bash
git switch main
```

or:

```bash
git switch master
```

Deploy again:

```bash
./deploy.sh
```

## Feynman explanation

Explain rollback like this:

```text
Rollback means I choose an older known-good version and deliver that version again.
```

## Questions

1. Which commit was deployed during rollback?
2. How did you verify the older content was served?
3. How did you restore the latest version?
4. Why is rollback part of deployment skill?

✅ Green check:

```text
[ ] I deployed an older version intentionally.
[ ] I restored the current version intentionally.
[ ] I verified both with curl.
```

---

# Final deployment journal

Complete this after the lab:

```text
Project:
Remote target:
URL:

Latest commit deployed:

Before deployment:
[ ] git status was clean
[ ] git log showed the intended commit
[ ] rsync dry run made sense

Verification:
[ ] curl returned expected content
[ ] remote files were in the expected directory
[ ] logs showed no error

What Git proved:

What rsync proved:

What curl proved:

What logs proved:

One mistake I almost made:

One habit I improved:
```

---

# Completion checkpoint

You are done only when you can explain these without notes:

1. Git does not deploy files by itself.
2. Git records project history.
3. `rsync` copies the current working tree files to the server.
4. A commit is a known version.
5. A clean working tree reduces deployment confusion.
6. `git diff` shows what changed before committing.
7. `rsync -n` previews server changes before making them.
8. `--delete` removes remote files that are not local.
9. `curl` verifies what the web server returns.
10. Logs verify what the server saw.
11. Branch switching is not deployment.
12. Rollback means deploying an older known-good version.

## Final Feynman test

Explain this in simple language:

```text
Why is Git useful before rsync deployment?
```

Good answer:

```text
Git lets me know exactly what version I am about to send. rsync sends the files. curl and logs prove the server is actually serving them.
```

Now explain it again using your own words.
