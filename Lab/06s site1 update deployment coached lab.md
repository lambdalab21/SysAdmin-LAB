# Site1 Update Deployment Lab (`scp`) — Coached Version

## Purpose

You already installed Nginx, opened the firewall, created the deployment user, and created this target directory:

```text
/var/www/site1/public/
```

This lab is **not** about setting up Nginx again.

This lab is about building disciplined deployment habits:

```text
change locally
→ predict what should change
→ deploy one small change
→ verify from browser/curl
→ inspect logs
→ write the lesson
```

Do not rush to “it showed in the browser.” That is only one piece of evidence.

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
My local project directory:

My server hostname or IP:

My remote target directory:

My test URL:
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
The source of truth is:

The server copy is:

If the local file and server file disagree, the correct one should be:
```

Expected idea:

```text
The local project is the source of truth.
The server receives deployed files.
The server should not be manually edited.
```

Now inspect local files:

```bash
cd ~/projects/site1
find ./public -type f -print
```

Inspect server files:

```bash
ssh deploy@app01 'find /var/www/site1/public -type f -print'
```

## Stop Point

Do not continue until you can answer:

```text
Which files exist locally?

Which files exist on the server?

Are there any server-only files?

Are there any local-only files?
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
I am changing this file:

The exact change is:

After deployment, I expect this URL to show the change:
```

Now save the file.

Inspect the local change:

```bash
grep -n 'Deployment practice' public/index.html
```

## Pay Attention

Do not deploy yet.

First prove that the change exists locally.

If you cannot find the change locally, deployment is not the next problem.

---

# Part 3: Predict the Deployment Command

You will copy one file first:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

Before running it, write:

```text
This command copies from:

This command copies to:

The local side is:

The remote side is:

The colon means:

If this succeeds, the changed file should appear at:
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
Exact command:

Exact output:

What this proves:

What this does not prove:
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
Did the server file contain the expected change?

What exact output proves it?

Who owns the file?

Can Nginx probably read it?
```

## Pay Attention

This verifies the file copy.

It still does not prove the browser received the new page.

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
HTTP status code:

Did curl show the updated text?

Did the browser show the updated text?

If browser and curl disagree, what might explain it?
```

Possible explanations:

```text
browser cache
wrong hostname
wrong server block
wrong file copied
Nginx root points somewhere else
```

## Stop Point

Do not say “deployment worked” until both are true:

```text
[ ] The server file contains the change.
[ ] HTTP response contains the change.
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

Write:

```text
Did my request appear in the access log?

What path did the browser request?

What status code did Nginx return?

Did the error log show anything new?
```

## Pay Attention

The browser showing the page is useful.

The log tells you what the server actually received and returned.

A good infrastructure person verifies both.

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
New local file:

Expected remote file:

Expected URL:
```

Deploy:

```bash
scp ./public/practice.html deploy@app01:/var/www/site1/public/
```

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

Write:

```text
What changed?

How did I verify the file exists?

How did I verify Nginx served it?

What did the access log show?
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
Does practice.html still exist on the server?

Why?

What does this prove about scp?

What problem could stale files cause?

What tool should solve this later?
```

Expected conclusion:

```text
scp copies files.
scp does not synchronize or delete stale files.
rsync with --delete is better for repeatable deployment.
```

---

# Part 10: Short Reflection — Toyota-Style

Do not skip this.

Write a short reflection. Keep it brief and evidence-based.

```text
Symptom or task:

What I expected:

What actually happened:

Strongest evidence:

One wrong assumption I avoided or corrected:

One command that gave useful evidence:

One thing I would do differently next deployment:
```

Example:

```text
Task:
Update index.html and deploy it.

What I expected:
The browser should show "Deployment practice version 2."

What actually happened:
The server file updated and curl showed the new text.

Strongest evidence:
grep on the server found the new line, and curl returned the same text.

One wrong assumption I avoided:
I did not assume scp success meant Nginx served the new file.

One useful command:
curl -s http://app01/ | grep 'Deployment practice'

One thing I would do differently:
Check logs immediately after curl so I connect request and server evidence.
```

---

# Completion Checkpoint

You are done only when you can answer these without guessing:

1. Which directory is the source of truth?
2. Which directory does Nginx serve?
3. What exactly did `scp` copy?
4. How did you prove the file changed on the server?
5. How did you prove Nginx served the changed file?
6. What did the access log show?
7. Did the error log show anything new?
8. Why does Nginx not need a restart for a static-file update?
9. What stale-file weakness did `scp` show?
10. Why will `rsync` be better for the next deployment phase?

---

# Habit Checklist

Before command:

```text
[ ] I know what this command tests.
[ ] I know what output I expect.
[ ] I know what file or system state should change.
```

After command:

```text
[ ] I quoted exact output or exact log evidence.
[ ] I wrote what it proves.
[ ] I wrote what it does not prove.
[ ] I chose the next command based on evidence.
```

If you skip these, you are not practicing deployment.

You are just typing.
