# Pre-Deployment Backup Lab for `site1`

## Purpose

You read **The Linux Command Line**, Ch. 18: **Archiving and Backup**.

Now apply it to a real deployment problem:

```text
Before I change the server, can I preserve the current working state?
```

This lab adds a disciplined backup step before updating `site1`.

The goal is not to collect random backup files.

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

Feynman check:

```text
If I deploy a bad version, the backup lets me put the old working files back.
```

If you cannot explain that simply, slow down before typing commands.

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
My local project directory:

My local public directory:

My server hostname or IP:

My remote public directory:

My backup directory:

My test URL:
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
What directory will deployment change?

What command will change it?

Can this command overwrite files?

Can this command delete files?

What would be painful to lose?
```

Expected idea:

```text
Deployment changes /var/www/site1/public/.
rsync --delete can overwrite and delete files there.
Therefore I should back up /var/www/site1/public/ before deploying.
```

## Green check

```text
[ ] I know the exact remote directory that may change.
[ ] I know whether my deployment command can delete files.
[ ] I know what I am backing up.
```

Do not continue until those are true.

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
Current server files:

Current homepage response contains:

What this proves:

What this does not prove:
```

Important:

```text
find proves files exist.
curl proves Nginx is serving something over HTTP.
Neither one alone proves the whole site is correct.
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

Write:

```text
Backup parent directory:

Who owns it?

What command proves deploy can write there?
```

## Green check

```text
[ ] Backup directory exists.
[ ] deploy can write into it.
[ ] I did not make the backup directory world-writable.
```

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

## Feynman check

Explain it simply:

```text
tar is putting the current server public directory into one compressed file.
The timestamp keeps each backup from overwriting the previous backup.
```

Write:

```text
Backup command:

Backup file created:

Backup size:

What directory was backed up?

What this proves:

What this does not prove:
```

Important:

```text
Creating a tar file does not prove it can restore correctly.
You must inspect or test the archive.
```

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

Write:

```text
Newest backup file:

Contents shown by tar -tzf:

Does the archive contain public/index.html?

Does it contain the expected site files?

What this proves:
```

## Green check

```text
[ ] The backup file exists.
[ ] The backup is not zero bytes.
[ ] tar -tzf can list the archive.
[ ] The archive contains public/index.html.
```

Do not deploy until all four are true.

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
Changed file:

Exact change:

Local verification output:
```

---

# Part 7: Predict the Deployment

Before deploying, write:

```text
Deployment command I plan to run:

Source path:

Destination path:

Can this command overwrite files?

Can this command delete files?

Which backup protects me if this goes wrong?

How would I restore from that backup?
```

Do not run the deployment command until you can answer this.

---

# Part 8: Deploy

Use `scp` if this is still `site1` practice:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

Or, if you are practicing the later `rsync` pattern:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/
```

Stop and read the dry-run output.

If the dry run is correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

Write:

```text
Exact command run:

What changed:

What did not change:

What could still be wrong:
```

Important:

```text
A successful transfer command does not prove Nginx served the new page.
```

---

# Part 9: Verify After Deployment

## Filesystem verification

```bash
ssh deploy@app01 'grep -n "Backup-before-deploy" /var/www/site1/public/index.html'
```

## HTTP verification

```bash
curl -s http://app01/ | grep 'Backup-before-deploy'
```

## Log verification

If `deploy` has permission:

```bash
ssh deploy@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
```

Otherwise use admin:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.access.log'
```

Check errors:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site1.error.log'
```

Write:

```text
Filesystem evidence:

HTTP evidence:

Access log evidence:

Error log evidence:

Final conclusion:
```

## Green check

```text
[ ] Server file contains the new text.
[ ] curl shows the new text.
[ ] Access log shows my request.
[ ] Error log shows no new relevant error.
[ ] I did not declare success from scp or rsync alone.
```

---

# Part 10: Restore Drill

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

Write:

```text
Restore test directory:

Files restored:

Did public/index.html appear?

What this proves:
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

## Stop and think

A restore command can overwrite the current site.

Before doing a real rollback, write:

```text
I am about to replace:

With files from:

The reason is:

The command can delete current files because:

My verification after rollback will be:
```

---

# Part 11: Clean Up Old Backups

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

# Part 12: Final Reflection

Write briefly.

```text
Task:

Directory protected by backup:

Backup file created:

Command that created the backup:

Command that verified the backup:

Deployment command:

Strongest evidence deployment worked:

Strongest evidence backup is usable:

One thing the backup does not protect against:

One mistake I avoided:

One habit I practiced:

One habit to improve next time:
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

1. Why back up before a risky deployment?
2. What directory did you back up?
3. What does `tar -czf` do?
4. What does `tar -tzf` do?
5. What does `tar -xzf` do?
6. Why use a timestamp in the backup filename?
7. Why verify the archive before deploying?
8. Why test restore into `/tmp` before overwriting the real site?
9. Why is Git not the same as a server backup?
10. Why is `scp` success not enough proof that the website works?
11. Why does `rsync --delete` make backup more important?
12. What exact evidence proved the backup existed?
13. What exact evidence proved the site still worked?
