# Site1 Update Deployment Lab (`scp`) — Coached Version

## Purpose

You already installed Nginx, opened the firewall, created the deployment user, and created this target directory:

```text
/var/www/site1/public/
```

This lab is about building disciplined deployment habits:

```text
change locally
→ predict what should change
→ deploy one small change
→ verify from browser/curl
→ inspect logs
→ write the lesson
```

---

## Current setup

Assume:

```text
Development machine project: ~/projects/site1/
Local public directory:      ~/projects/site1/public/
Server:                      app01
Deploy user:                 deploy
Remote public directory:     /var/www/site1/public/
Nginx site URL:              http://app01/
```

If your actual paths differ, write them here:

```text
My local project directory: ~/code/projects/site1/public

My server hostname or IP: 192.168.0.107

My remote target directory: 

My test URL: http://127.0.0.1:8080/
```

---

## Rules

1. Edit files on the development machine.
2. Do not edit website files directly on `app01`.
3. Do not deploy as `root`.
4. Use `scp` in this lab.
5. Before each command, write what you expect.
6. After each command, write what the output proves.
7. Verify with at least two kinds of evidence:
   - filesystem evidence
   - HTTP/browser evidence
   - log evidence

---

# Part 1: Stop and Identify the Source of Truth

Before touching files, answer this.

```text
The source of truth is: The local project directory: ~/code/projects/site1/public/

The server copy is: a deployed copy, not the source. 

If the local file and server file disagree, the correct one should be: The local version. 
```

Expected idea:

```text
The local project is the source of truth.
The server receives deployed files.
The server should not be manually edited.
```

Now inspect local files:

```bash
cd ~/code/projects/site1
find ./public -type f -print
```

Inspect server files:

```bash
ssh deploy@app01 'find /var/www/site1/public -type f -print'
```

## Stop Point

Do not continue until you can answer:

```text
Which files exist locally? index.html, style.css, form.scss, java.js, signupform.js

Which files exist on the server? Mirror of deployed public contents.
```

---

# Part 2: Make One Small Local Change

Edit only one file first.

Example:

```bash
vi public/index.html
```

Make a visible change, such as:

```html
<p>Deployment practice version 2.</p>
```

Before saving, write:

```text
I am changing this file: public/index.html

The exact change is: <p> Deployment Practice Version 2</p> 
```

Now save the file.

Inspect the local change:

```bash
grep -n 'Deployment practice' public/index.html
```

---

# Part 3: Predict the Deployment Command

You will copy one file first:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

Before running it, write:

```text
This command copies from: Local ./public/idnex.html

This command copies to: deploy@192.168.0.107:/var/www/site1/public

The local side is: Development machine. 

The remote side is: app01 server. 

The colon means: Separates remote user/host from remote path. 

If this succeeds, the changed file should appear at:
/code/projects/site1/public/
```

## Stop Point

Do not run the command until you can point to:

```text
./public/index.html
```

as the source and:

```text
deploy@app01:/var/www/site1/public/
```

as the destination.

---

# Part 4: Deploy One File

Run:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

After running it, do **not** write only “worked.”

Write:

```text
Exact command: scp ./public/index.html
deploy@192.168.0.107:/var/www/site1/public

What this proves: File was successfully copied over SSH to the correct server path. 

What this does not prove: That nginx has reloaded/served the new content, or that other files are in sync. 
```

Example:

```text
What this proves:
scp authenticated over SSH and copied index.html to the server path.

What this does not prove:
It does not prove Nginx served the new file yet.
It does not prove CSS or other pages were updated.
It does not prove old server-only files were removed.
```

---

# Part 5: Verify the Server File Directly

Check the file on `app01`:

```bash
ssh deploy@app01 'grep -n "Deployment practice" /var/www/site1/public/index.html'
```

Also inspect timestamp and ownership:

```bash
ssh deploy@app01 'ls -l /var/www/site1/public/index.html'
```

Write:

```text
Did the server file contain the expected change? Yes. 

What exact output proves it? grep on the server showed the new text. 

Who owns the file? deploy user

Can Nginx probably read it? Yes. 
```


---

# Part 6: Verify Through HTTP

Use `curl`:

```bash
curl -v http://app01/
```

If the output is long, search:

```bash
curl -s http://app01/ | grep 'Deployment practice'
```

Then open the browser:

```text
http://app01/
```

Write:

```text
HTTP status code: 200

Did curl show the updated text? Yes. 

Did the browser show the updated text? Yes. 

If browser and curl disagree, what might explain it? Browser cache, wrong URL, or Nginx configuration pointing somewhere else. 
```

Possible explanations:

```text
browser cache
wrong hostname
wrong server block
wrong file copied
Nginx root points somewhere else
```
---

# Part 7: Inspect Nginx Logs

On `app01`, check recent access logs:

```bash
ssh deploy@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
```

If `deploy` cannot use `sudo`, log in with your admin user:

```bash
ssh your-admin-user@app01
sudo tail -n 20 /var/log/nginx/site1.access.log
```

Check error logs:

```bash
sudo tail -n 20 /var/log/nginx/site1.error.log
```
No errors
Write:

```text
Did my request appear in the access log? Yes. 

What path did the browser request? / or /index.html

What status code did Nginx return? 200 

Did the error log show anything new? No errors at all, thus no new errors either. 
```


---

# Part 8: Deploy a New Page

Create a new page locally:

```bash
vi public/practice.html
```

Example content:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Practice Page</title>
</head>
<body>
  <h1>Practice Page</h1>
  <p>This page was deployed with scp.</p>
</body>
</html>
```

Before deploying, write:

```text
New local file: public/practice.html

Expected remote file: Same path on server. 

Expected URL: http://app01/practice.html
```

Deploy:

```bash
scp ./public/practice.html deploy@app01:/var/www/site1/public/
```
^ pr 100% 194 91.7KB/s 00:00

Verify filesystem:

```bash
ssh deploy@app01 'ls -l /var/www/site1/public/practice.html'
```

Verify HTTP:

```bash
curl -v http://app01/practice.html
```

Verify log:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
```

---

# Part 9: Notice the Weakness of `scp`

Now delete the page locally:

```bash
rm public/practice.html
```

Confirm it is gone locally:

```bash
find ./public -type f -print
```

Deploy the whole public directory with `scp`:

```bash
scp -r ./public/. deploy@app01:/var/www/site1/public/
```

Now check the server:

```bash
ssh deploy@app01 'ls -l /var/www/site1/public/practice.html'
```

Test:

```bash
curl -v http://app01/practice.html
```

Write:

```text
Does practice.html still exist on the server? Yes. 

Why? scp -r only copies and adds files. it does not delete removed local files. 

What does this prove about scp? it is a simple copy tool and not a full synchronizer. 

What problem could stale files cause? Outdated or sensitive content remaining alive. 

What tool should solve this later? rsync --delete for proper synchrnoizeation. 
```

Expected conclusion:

```text
scp copies files.
scp does not synchronize or delete stale files.
rsync with --delete is better for repeatable deployment.
```

---

# Part 10: Short Reflection


Write a short reflection. Keep it brief and evidence-based.

```text
Symptom or task: Update index.html and deploy a new page with scp. 

What I expected: Clean update visible in browser and curl. 

What actually happened: File copied successfully. File was served after verification. 

Strongest evidence: server grep and curl output both showed changes. 

One wrong assumption I avoided or corrected: Assuming scp success automatically means that nginx was serving new contents. 

One command that gave useful evidence: curl -s https://app01 | grep 'Deployment practice' and server ls -l. 

One thing I would do differently next deployment: Always verify with both filesystem check and https/logs/immediately. Use rsync for future deploys.
```
