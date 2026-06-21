# Phase 2 Lab: Deploy `site2` with `rsync`

## Purpose

You already deployed `site1` with `scp`.

Keep `site1` as the **scp baseline**.

Use this lab to deploy a new site:

```text
site1 = copied with scp
site2 = synchronized with rsync
```

This makes the comparison clear.

The goal is not merely to run `rsync`.

The goal is to build this habit:

```text
question → command → evidence → conclusion → next narrow question
```

Do not rush through commands.

If you cannot explain what a command is testing, do not run it yet.

---

# Mental Model: Feynman Version

Imagine the server directory is a display shelf.

`scp` is like carrying items one by one to the shelf.

If you stop carrying one item, the old item may still remain on the shelf.

`rsync` is closer to saying:

```text
Make the shelf match this box of items.
```

But that power is dangerous.

With `--delete`, `rsync` can remove things from the shelf if they are not in your local box.

So before using it for real, you ask:

```text
What would rsync add?
What would rsync update?
What would rsync delete?
Is the destination path exactly correct?
```

That is why dry run matters.

---

# Anti-Rushing Rule

Before each important command, write:

```text
I am about to run:

This command tests:

If I am right, I expect:

If I am wrong, I expect:
```

After each important command, write:

```text
Exact output or exact evidence:

What this proves:

What this does not prove:

Next narrow question:
```

Do not write only:

```text
worked
failed
done
probably
```

Those are not disciplined conclusions.

---

# Green Check Habit

Use checkboxes, but do not check them mindlessly.

A checkbox is earned only when you have evidence.

Example:

```text
[ ] Dry run output inspected carefully
```

You may check it only after you can say:

```text
The dry run would copy these files:
The dry run would delete these files:
The destination path is:
This is safe because:
```

Small green checks are useful only if they represent real understanding.

---

# Starting Assumptions

Already done:

```text
Nginx is installed.
Nginx is running.
The firewall allows HTTP.
The deployment user exists.
/var/www/site1/public exists.
site1 works.
```

This lab creates:

```text
/var/www/site2/public/
~/projects/site2/public/
```

---

# Part 1: Decide Why `site2` Exists

Stop here.

Explain before typing commands:

```text
Why not overwrite site1 immediately?
```

Answer:

```text
site1 is the scp baseline.
site2 is the rsync experiment.
Keeping both lets me compare deployment methods without confusing the results.
```

Green check:

```text
[ ] I can explain why site1 remains the scp baseline.
[ ] I can explain why site2 is the rsync experiment.
```

---

# Part 2: Create the Local `site2` Directory

On the development machine:

```bash
mkdir -p ~/projects/site2/public
cd ~/projects/site2
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
  <title>site2</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>site2</h1>
  <p>This site is deployed with rsync.</p>
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
  max-width: 700px;
  margin: 3rem auto;
  line-height: 1.5;
}
```

Inspect:

```bash
find ./public -type f -print
```

Stop and answer:

```text
What files are in the source directory?

Which directory is the source of truth?

Should I edit files directly on app01 after this?
```

Expected conclusion:

```text
~/projects/site2/public/ is the source of truth.
app01 receives deployed files.
Do not edit deployed files manually on app01.
```

Green check:

```text
[ ] I inspected the local source files.
[ ] I know which directory is the source of truth.
[ ] I will not manually edit site2 files on app01.
```

---

# Part 3: Create the Remote Target Directory

On `app01`, create the target directory.

```bash
ssh your-admin-user@app01
sudo mkdir -p /var/www/site2/public
sudo chown -R deploy:nginx /var/www/site2
sudo chmod -R 2755 /var/www/site2
ls -ld /var/www/site2
ls -ld /var/www/site2/public
exit
```

Slow down at ownership.

Explain like you are teaching a younger student:

```text
deploy owns the files because deploy writes during deployment.
nginx is the group because Nginx needs to read the files.
Nginx should not need write permission to static website files.
```

Green check:

```text
[ ] deploy can write to /var/www/site2/public.
[ ] nginx can read from /var/www/site2/public.
[ ] I can explain why nginx should not own the deployment process.
```

Optional evidence commands:

```bash
ssh deploy@app01 'test -w /var/www/site2/public && echo deploy-can-write'
ssh your-admin-user@app01 'sudo -u nginx test -r /var/www/site2/public && echo nginx-can-read-directory'
```

---

# Part 4: Configure Nginx for `site2`

On `app01`:

```bash
ssh your-admin-user@app01
sudo vi /etc/nginx/conf.d/site2.conf
```

Use:

```nginx
server {
    listen 80;
    server_name site2.local;

    root /var/www/site2/public;
    index index.html;

    access_log /var/log/nginx/site2.access.log;
    error_log  /var/log/nginx/site2.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Test before reload:

```bash
sudo nginx -t
```

Only if the test succeeds:

```bash
sudo systemctl reload nginx
```

Stop and answer:

```text
What does nginx -t prove?

What does it not prove?
```

Expected answer:

```text
nginx -t proves that the Nginx configuration syntax is valid.
It does not prove that the website files exist or that the browser can reach the site.
```

Green check:

```text
[ ] I ran nginx -t before reload.
[ ] I did not reload a broken config.
[ ] I understand that config syntax and website content are separate.
```

Exit:

```bash
exit
```

---

# Part 5: Read the `rsync` Options Before Using Them

On the development machine:

```bash
man rsync
```

Search for:

```text
/--archive
/--verbose
/--dry-run
/--delete
/trailing slash
```

Quit:

```text
q
```

Write short answers:

```text
-a means:

-v means:

-n means:

--delete means:

Trailing slash matters because:
```

Minimum expected answers:

```text
-a preserves important file attributes and copies recursively.
-v prints more detail.
-n is dry run; it shows what would happen without changing files.
--delete removes destination files that are not in the source.
The trailing slash changes whether rsync copies the directory itself or the contents of the directory.
```

Green check:

```text
[ ] I can explain -a.
[ ] I can explain -v.
[ ] I can explain -n.
[ ] I can explain --delete.
[ ] I can explain the trailing slash problem.
```

---

# Part 6: First Dry Run

From the development machine:

```bash
cd ~/projects/site2
```

Run dry run:

```bash
rsync -avn ./public/ deploy@app01:/var/www/site2/public/
```

Stop.

Do not run the real command yet.

Answer:

```text
What is the source path?

What is the destination path?

What files would be copied?

Would anything be deleted?

Is this the site2 directory, not site1?

Is the trailing slash correct?
```

Green check:

```text
[ ] I confirmed the source is ./public/.
[ ] I confirmed the destination is /var/www/site2/public/.
[ ] I confirmed this is not site1.
[ ] I inspected the dry run output.
[ ] I did not run the real command before understanding the dry run.
```

---

# Part 7: Deploy for Real

Only if the dry run made sense:

```bash
rsync -av ./public/ deploy@app01:/var/www/site2/public/
```

Verify remote files:

```bash
ssh deploy@app01 'find /var/www/site2/public -type f -print'
```

Test with Host header:

```bash
curl -v -H 'Host: site2.local' http://app01/
```

Stop and explain:

```text
What changed on app01?

What proves it?

Did Nginx need to restart?

Why or why not?
```

Expected conclusion:

```text
The static files changed on disk.
Nginx serves files from disk, so static file updates do not require an Nginx restart.
```

Green check:

```text
[ ] Remote files exist.
[ ] curl returns the site2 page.
[ ] I verified with the correct Host header.
[ ] I did not restart Nginx unnecessarily.
```

---

# Part 8: Observe Incremental Copying

Edit only one local file:

```bash
vi ./public/index.html
```

Change:

```html
<h1>site2</h1>
```

to:

```html
<h1>site2 version 2</h1>
```

Dry run:

```bash
rsync -avn ./public/ deploy@app01:/var/www/site2/public/
```

Stop and answer:

```text
Which file would rsync transfer?

Did it list every file or only changed files?

What does this teach me about rsync?
```

Then deploy:

```bash
rsync -av ./public/ deploy@app01:/var/www/site2/public/
```

Verify:

```bash
curl -s -H 'Host: site2.local' http://app01/ | grep 'site2 version 2'
```

Green check:

```text
[ ] I predicted the changed file before deploying.
[ ] I verified the changed content after deploying.
[ ] I understand that rsync is useful for repeated deployment.
```

---

# Part 9: Stale File Experiment

Create a local temporary page:

```bash
echo 'temporary page' > ./public/old-page.html
```

Deploy:

```bash
rsync -av ./public/ deploy@app01:/var/www/site2/public/
```

Verify:

```bash
curl -s -H 'Host: site2.local' http://app01/old-page.html
```

Now delete it locally:

```bash
rm ./public/old-page.html
```

Deploy again without `--delete`:

```bash
rsync -av ./public/ deploy@app01:/var/www/site2/public/
```

Check:

```bash
curl -v -H 'Host: site2.local' http://app01/old-page.html
```

Stop and explain:

```text
Why does old-page.html still exist on the server?

How is this similar to scp?

What does this teach me about deployment?
```

Feynman explanation:

```text
rsync without --delete brings new and changed items to the shelf,
but it does not remove old items from the shelf.
```

Green check:

```text
[ ] I created a stale server file.
[ ] I proved it still exists after normal rsync.
[ ] I understand why --delete exists.
```

---

# Part 10: Use `--delete` Carefully

Before using `--delete`, dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

Stop.

Do not deploy yet.

Write:

```text
Files that would be deleted:

Files that would be copied or updated:

Destination path:

Why this is safe:
```

Only if the output is correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site2/public/
```

Verify stale file is gone:

```bash
curl -v -H 'Host: site2.local' http://app01/old-page.html
```

Expected:

```text
404 Not Found
```

Green check:

```text
[ ] I dry-ran before using --delete.
[ ] I identified exactly what would be deleted.
[ ] I confirmed the destination path before deleting.
[ ] I verified the stale page returns 404.
```

---

# Part 11: Trailing Slash Local Experiment

Do this locally, not on the server.

```bash
rm -rf ~/rsync-lab
mkdir -p ~/rsync-lab/source
mkdir -p ~/rsync-lab/dest
printf 'test\n' > ~/rsync-lab/source/file.txt
```

Experiment A:

```bash
rsync -av ~/rsync-lab/source/ ~/rsync-lab/dest/
find ~/rsync-lab -type f -print
```

Reset:

```bash
rm -rf ~/rsync-lab/dest
mkdir -p ~/rsync-lab/dest
```

Experiment B:

```bash
rsync -av ~/rsync-lab/source ~/rsync-lab/dest/
find ~/rsync-lab -type f -print
```

Stop and answer:

```text
With source/, the resulting path was:

With source, the resulting path was:

The difference is:

Why this matters with --delete:
```

Green check:

```text
[ ] I saw the path difference myself.
[ ] I can explain trailing slash without notes.
[ ] I understand why trailing slash plus --delete requires caution.
```

---

# Part 12: Create `deploy.sh`

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
REMOTE_DIR="/var/www/site2/public/"
HOST_HEADER="site2.local"

echo "Dry run:"
rsync -avn --delete \
  "$LOCAL_DIR" \
  "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
read -r -p "Deploy for real? [y/N] " answer

if [[ "$answer" == "y" || "$answer" == "Y" ]]; then
  rsync -av --delete \
    "$LOCAL_DIR" \
    "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

  echo
  echo "Verifying HTTP response..."
  curl -s -H "Host: $HOST_HEADER" "http://$REMOTE_HOST/" | head

  echo
  echo "Deployment complete."
else
  echo "Deployment cancelled."
fi
```

Make executable:

```bash
chmod +x deploy.sh
```

Run:

```bash
./deploy.sh
```

Slow down before pressing `y`.

Answer:

```text
Did the dry run show the expected destination?

Did it show unexpected deletes?

Am I deploying site2, not site1?
```

Green check:

```text
[ ] deploy.sh uses site2 paths.
[ ] deploy.sh shows dry run first.
[ ] deploy.sh asks before deploying for real.
[ ] deploy.sh verifies with curl.
```

---

# Part 13: Compare `scp` and `rsync`

Fill in:

| Behavior | scp | rsync |
|---|---|---|
| Uses SSH | | |
| Copies files | | |
| Copies directories | | |
| Dry run | | |
| Transfers changed files efficiently | | |
| Can delete stale destination files | | |
| Better for one-time copy | | |
| Better for repeatable deployment | | |

Feynman explanation:

```text
Explain scp and rsync to a 10-year-old using the shelf analogy.
```

Write 3 sentences:

```text
scp is like...
rsync is like...
--delete is dangerous because...
```

---

# Part 14: Final Reflection

Use this short report.

```text
Symptom or task:

Source directory:

Destination directory:

Strongest evidence that deployment worked:

One thing dry run prevented:

One stale-file lesson:

One trailing-slash lesson:

One mistake I almost made:

One habit I improved:
```

Keep it short.

Good lab work is not long.

Good lab work is precise.

---

# Completion Checkpoint

Do not move on until you can explain these without notes:

```text
rsync copies differences between source and destination.
-n is dry run.
--delete removes destination files missing from the source.
Dry run should come before --delete.
The local public/ directory is the source of truth.
The server should not be edited manually.
The trailing slash changes the meaning of the source path.
Nginx does not need to restart for static file changes.
deploy writes the files; nginx reads the files.
site1 is the scp baseline; site2 is the rsync deployment.
```

Final green checks:

```text
[ ] site1 still works.
[ ] site2 works.
[ ] site2 was deployed with rsync.
[ ] stale files can be removed with --delete after dry run.
[ ] I can explain the trailing slash difference.
[ ] I can explain scp vs rsync using a simple analogy.
[ ] I did not rush through the lab mindlessly.
```

