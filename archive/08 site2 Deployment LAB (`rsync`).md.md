#deployment #LAB 
# Phase 2 Lab: Deploy `site2` with `rsync`

```text
question → command → evidence → conclusion → next narrow question
```
# Part 1: Decide Why `site2` Exists

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
What files are in the source directory? public/index.html and public/style.css

Which directory is the source of truth? ~/projects/site2/public on the development machine. 

Should I edit files directly on app01 after this? No, always edit files locally and deploy. 
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
What does nginx -t prove? That the configuration syntax is valid. 

What does it not prove? That the ifles exist or that the site is reachable in a browser. 
```

Expected answer:

```text
nginx -t proves that the Nginx configuration syntax is valid.
It does not prove that the website files exist or that the browser can reach the site.
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
-a means: Archive Mode. 

-v means: Verbose. 

-n means: Dry Run. 

--delete means: Delete Files in Destination that are missing from the source. 

Trailing slash matters because: Source/ copies the contents of source into destinations. Source copies the source directory itself. 
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

Answer:

```text
What is the source path? ./public/

What is the destination path? deploy@192.168.0.107:/var/www/site2/public/

What files would be copied? index.html and style.css

Would anything be deleted? No. 

Is this the site2 directory, not site1? Yes. 

Is the trailing slash correct? Yes. 
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
What changed on app01? Static files were updated on disk. 

What proves it? Find shows files and curl returns site2 content. 

Did Nginx need to restart? No. NGinx serves static files from disk. 

Why or why not? NGinx serves static files from disk. 
```

Expected conclusion:

```text
The static files changed on disk.
Nginx serves files from disk, so static file updates do not require an Nginx restart.
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
Which file would rsync transfer? Only the changed `index.html`

Did it list every file or only changed files? No. Just the changed files. 

What does this teach me about rsync? RSync is effecient. It only transfers differences. 
```

Then deploy:

```bash
rsync -av ./public/ deploy@app01:/var/www/site2/public/
```

Verify:

```bash
curl -s -H 'Host: site2.local' http://app01/ | grep 'site2 version 2'
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
Why does old-page.html still exist on the server? --delete wasn't used, so rsync only adds on and updates assets. 

How is this similar to scp? Yes. 

What does this teach me about deployment? It explicitly manages deletions with `--delete` for clean deployments. 
```

---

# Part 10: Use `--delete` Carefully

Before using `--delete`, dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site2/public/
```

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
With source/, the resulting path was: Files go diretly into `dest/`. 

With source, the resulting path was: It creates dest/source/

The difference is: It trails slash controls whether the source directory or it's contents are copied. 

Why this matters with --delete: Wrong slashes can delete wrong files or leave extra directories. 
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
Did the dry run show the expected destination? It should show the correct destination and only intended changes. 
```

---

# Part 13: Compare `scp` and `rsync`

Fill in:

| Behavior                            | scp | rsync |
| ----------------------------------- | --- | ----- |
| Uses SSH                            | Yes | Yes   |
| Copies files                        | Yes | Yes   |
| Copies directories                  | Yes | Yes   |
| Dry run                             | No  | Yes   |
| Transfers changed files efficiently | No  | Yes   |
| Can delete stale destination files  | No  | Yes   |
| Better for one-time copy            | Yes | -     |
| Better for repeatable deployment    | Yes | -     |

Write 3 sentences:

```text
scp is like mailing a full package every time. 
rsync is like smart syncing that only sends changes. 
--delete is dangerous because it can remove files you forgot were only on the server. 
```

---

# Part 14: Final Reflection

Use this short report.

```text
Source directory: ~/projects/site2/public/

Destination directory: /var/www/site2/public/

Strongest evidence that deployment worked: curl showed updated content and correct Host header. 

One thing dry run prevented: Accidental deletion of files. 

One stale-file lesson: Always uses `--delete` after dry runs. 

One trailing-slash lesson: Controls contents and directory copies. 

One mistake I almost made: Editing files directly on the server. 

One habit I improved: Always dry-run first. 
```

Keep it short.

Good lab work is not long.

Good lab work is precise.

---