# Pre-Deployment Backup Lab for `site1`

## Purpose

You read **The Linux Command Line**, Ch. 18: **Archiving and Backup**.

This lab adds a backup step before updating `site1`.

The goal is to build this habit:

```text
know what will change
→ back up the current state
→ verify the backup
→ deploy
→ verify the site
→ know how to roll back
```

---

# Should You Back Up `site1` Before Deploying?

## For simple `scp` practice

A backup is useful for learning, but not always required.

If you copy only one harmless file, such as:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

then a backup is not strictly necessary every time.

But for this stage, do it anyway for practice.

## For `rsync --delete`

A backup is strongly recommended before running:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

Why?

Because `--delete` can remove files from the destination.

If your source path or destination path is wrong, you can delete more than you intended.

## Rule for now

Use this rule:

```text
If a deployment can overwrite many files or delete server files,
make a backup first.
```

For this lab:

```text
Yes, back up site1 before deploying.
```

---

# Mental Model

Explain this in simple words.

```text
The local project is the source of truth.
The server directory is the currently running copy.
A backup is a snapshot of the current server copy before I replace it.
```

---

# Current Setup

Assume:

```text
Development machine project: ~/projects/site1/
Local public directory:      ~/projects/site1/public/
Server:                      app01
Deploy user:                 deploy
Remote public directory:     /var/www/site1/public/
Backup directory on server:  /var/www/backups/site1/
Test URL:                    http://app01/
```

Write your actual values:

```text
My local project directory: ~/projects/site1

My local public directory: ~/projects/site1/public

My server hostname or IP: 192.168.0.107 or app01

My remote public directory: /var/www/site1/public/

My backup directory: /var/www/backups/site1/

My test URL: http://192.168.0.107
```

---

# Rules

1. Do not deploy as `root`.
2. Do not edit website files directly on `app01`.
3. Back up before a risky deployment.
4. Verify the backup exists before deploying.
5. A backup you cannot restore is not a backup.
6. Write exact paths, not vague words like “the file” or “the server.”
7. Do not check a box unless you can point to evidence.

---

# Part 1: Stop and Define What Might Change

Before creating a backup, answer this.

```text
What directory will deployment change? /var/www/site1/public/

What command will change it? rsync -av --delete or scp

Can this command overwrite files? Yes. 

Can this command delete files? Yes. (with --delete)

What would be painful to lose? The live website files served by NGinx. 
```

Expected idea:

```text
Deployment changes /var/www/site1/public/.
rsync --delete can overwrite and delete files there.
Therefore I should back up /var/www/site1/public/ before deploying.
```

---

# Part 2: Inspect the Current Server State

From the development machine:

```bash
ssh deploy@app01 'find /var/www/site1/public -type f -print | sort'
```

Also check HTTP:

```bash
curl -s http://app01/ | head
```

Write:

```text
Current server files: form.scss, index.html, java.js, practice.html, signupform.js, style.css

Current homepage response contains: Everything intended to be on it.

What this proves: That it's working as intended. 
```

Important:

```text
find proves files exist.
curl proves Nginx is serving something over HTTP.
Neither one alone proves the whole site is correct.
```

---

# Part 2.5: Quick Log Baseline Before Changing the Server

This is a short log check, not a full logging lab.

You are asking one narrow question:

```text
Is Nginx serving site1 normally before I change anything?
```

From the development machine, make one request:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://app01/
```

Expected:

```text
200
```
^working as expected
Now check the recent access log:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.access.log'
```

If that file does not exist, use:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/access.log'
```

Check the recent error log:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/site1.error.log'
```

If that file does not exist, use:

```bash
ssh your-admin-user@app01 'sudo tail -n 10 /var/log/nginx/error.log'
```

Write:

```text
HTTP status before deployment: 200

Any new error-log line? No.

What this proves: NGinx is serving site1 normally before changes. 

What this does not prove: That every file and feature on the site works perfectly. 
```

---


# Part 3: Create a Backup Directory

On `app01`, create a backup directory.

Use your admin user if `deploy` cannot create `/var/www/backups`.

```bash
ssh your-admin-user@app01
sudo mkdir -p /var/www/backups/site1
sudo chown -R deploy:deploy /var/www/backups/site1
exit
```

Verify as `deploy`:

```bash
ssh deploy@app01 'test -w /var/www/backups/site1 && echo "deploy can write backups"'
```
^ works

---

# Part 4: Create a Timestamped Backup with `tar`

On the development machine, run this remote command:

```bash
ssh deploy@app01 '
set -e
timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf /var/www/backups/site1/site1-public-$timestamp.tar.gz -C /var/www/site1 public
ls -lh /var/www/backups/site1/site1-public-$timestamp.tar.gz
'
```

## Stop and think

Look carefully at this part:

```bash
-C /var/www/site1 public
```

It means:

```text
Change to /var/www/site1,
then archive the public directory.
```

It avoids storing the full absolute path inside the archive.

---

# Part 5: Verify the Backup Contents

List backup files:

```bash
ssh deploy@app01 'ls -lt /var/www/backups/site1 | head'
```

List the contents of the newest archive:

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
echo "$latest"
tar -tzf "$latest" | head -n 20
'
```

Expected contents should look like:

```text
public/
public/index.html
public/style.css
...
```

---

# Part 6: Make One Local Change

On the development machine:

```bash
cd ~/projects/site1
```

Edit:

```bash
vi public/index.html
```

Add a visible line:

```html
<p>Backup-before-deploy practice.</p>
```

Verify locally:

```bash
grep -n 'Backup-before-deploy' public/index.html
```

Write:

```text
Changed file: /projects/site1/public/index.html

Exact change: Line 17 - <p>Backup-before-deploy practice.</p>

Local verification output: 17: <p>Backup-before-deploy practice.</p>
```

---

# Part 7: Deploy

Use `scp` if this is still `site1` practice:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```
^this one
Or, if you are practicing the later `rsync` pattern:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/
```

Stop and read the dry-run output.

If the dry run is correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

---

# Part 8: Verify After Deployment

You need three kinds of evidence:

```text
filesystem evidence
HTTP evidence
log evidence
```

## Filesystem verification

```bash
ssh deploy@app01 'grep -n "Backup-before-deploy" /var/www/site1/public/index.html'
```

This answers:

```text
Did the changed file reach the server filesystem?
```

## HTTP verification

```bash
curl -s -o /tmp/site1-after.html -w "%{http_code}\n" http://app01/
grep 'Backup-before-deploy' /tmp/site1-after.html
```

This answers:

```text
Did Nginx serve the changed content over HTTP?
```

## Log verification

Check the access log:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
```

If that file does not exist:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
```

Check the error log:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.error.log'
```

If that file does not exist:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

---

# Part 9: Restore Drill

Do this drill at least once while the site is simple.

The purpose is to learn whether your backup is useful.

## Find the newest backup

```bash
ssh deploy@app01 '
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
echo "$latest"
'
```

## Restore to a temporary test directory first

Do not overwrite the real site yet.

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

## Optional real rollback

Only do this if you intentionally want to roll back the running site.

```bash
ssh deploy@app01 '
set -e
latest=$(ls -t /var/www/backups/site1/site1-public-*.tar.gz | head -n 1)
rm -rf /tmp/site1-rollback
mkdir -p /tmp/site1-rollback
tar -xzf "$latest" -C /tmp/site1-rollback
rsync -av --delete /tmp/site1-rollback/public/ /var/www/site1/public/
'
```

Verify:

```bash
curl -s http://app01/ | head
```

---

# Part 10: Clean Up Old Backups

Do not let backups grow forever.

List backups:

```bash
ssh deploy@app01 'ls -lh /var/www/backups/site1'
```

For practice, keep the newest 5 backups.

Dry-run list of backups older than 5 newest:

```bash
ssh deploy@app01 '
ls -t /var/www/backups/site1/site1-public-*.tar.gz | tail -n +6
'
```

Only if the list makes sense, delete them:

```bash
ssh deploy@app01 '
ls -t /var/www/backups/site1/site1-public-*.tar.gz | tail -n +6 | xargs -r rm -v
'
```

## Stop and think

Before deleting backups, ask:

```text
Am I deleting backup files only?
Did I list them first?
Do I still have enough recent backups?
```

Do not run deletion commands casually.

---

# Part 11: Final Reflection

Write briefly.

```text
Task: Pre-deployment backup and safe update of site1. 

Directory protected by backup: /var/www/site1/public/

Command that created the backup: tar -czf ... -C /var/www/site1 public

Command that verified the backup: tar -tzf "$latest" | head

Deployment command: scp ./public/index.html deploy@app01:/var/www/site1/public/

Strongest evidence deployment worked: grep found the new text on the server and curl showed the paragraph. 

Access log evidence after deployment: New 200 entry for GET / from my IP. 

Error log evidence after deployment:No new errors. 

Strongest evidence backup is usable: Successfully extracted archive to /tmp and saw original files. 

One thing the backup does not protect against: Changes made directly on the server or files outside /var/www/site1/public/

One mistake I avoided: Deploying without backup. 

One habit I practiced: Backup -> Verify -> Deploy -> Verify -> Rollback. 

One habit to improve next time: Automate backup verification steps. 
```

## Good answer pattern

```text
The backup protects the current server copy of site1.
It does not replace Git.
It does not protect files outside /var/www/site1/public/.
It is useful before a risky deployment because it gives me a rollback point.
```

---

# Key Lessons

```text
Git protects source history.
tar backup protects the current server state.
scp copies files.
rsync can synchronize and delete stale files.
curl proves HTTP behavior.
logs prove what Nginx saw.
```

Do not confuse these tools.

Each answers a different question.

---

# Completion Checkpoint

Explain without notes:

1. Why back up before a risky deployment? To have a rollback point if something goes wrong, especially with --delete. 
2. What directory did you back up? /var/www/site1/public/
3. What does `tar -czf` do? It creates a compressed archive of files/directories. 
4. What does `tar -tzf` do? Lists the contents of a compressed archive without extracting. 
5. What does `tar -xzf` do? Extracts files from a compressed archive. 
6. Why use a timestamp in the backup filename? To uniquely identify backups and keep multiple versions. 
7. Why verify the archive before deploying? To confirm the backup actually contains the expected files and is not corrupt. 
8. Why test restore into `/tmp` before overwriting the real site? To safely validate the backup works without risking the live site. 
9. Why is Git not the same as a server backup? Git tracks source code history locally. Server backup captures the exact deployed state. 
10. Why is `scp` success not enough proof that the website works? It only proves the file reached the filesystem. It does not prove NGinx is serving it correctly over HTTP. 
11. Why does `rsync --delete` make backup more important?It can remove files on a server that no longer locally exists. 
12. What exact evidence proved the backup existed? ls -lt /var/www/backups/site1/ showed the .tar.gz file with correct size/timestamps. 
13. What exact evidence proved the site still worked? Curl returned 200 and contained the expected new content. 
14. What access-log line proved Nginx received the post-deployment request? The new GET / entry with 200 status after deployment. 
15. What did the error log show after deployment? No new error entries, site continued working well. 
