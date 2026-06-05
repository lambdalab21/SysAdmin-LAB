
# Phase 2 Lab: Deploy `site1` with `rsync`

## Goal

Replace the manual `scp` deployment with a repeatable `rsync` deployment.

The site is already working:

```text
development machine
        ↓
     app01
        ↓
/var/www/site1/public/
        ↓
      Nginx
```

Now learn how to make the server directory match the local deployment directory safely.

---

# Learning goals

By the end of this phase, explain:

1. What `rsync` does differently from `scp`.
    
2. Why a dry run is important.
    
3. What `--delete` does.
    
4. Why `--delete` can be dangerous.
    
5. Why the trailing slash matters.
    
6. How to detect stale files on the server.
    
7. Why deployment should come from a clean local directory.
    
8. How to verify deployment without editing the server manually.
    

---

# Part 1: Confirm the Starting State

On the development machine:

```bash
cd ~/projects/site1
find ./public -type f -print
```

Expected files may include:

```text
./public/index.html
./public/style.css
./public/about.html
```

On `app01`:

```bash
ssh deploy@app01
find /var/www/site1/public -type f -print
exit
```

Compare the two lists.

## Questions

1. Do the local and remote directories contain the same files?
    
2. Which directory is the source of truth?
    
3. Should files ever be edited manually on `app01` after deployment begins?
    

Correct idea:

```text
The local public/ directory is the source of truth.
The server receives deployed files.
```

---

# Part 2: Read the `rsync` Manual Page

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
```

Also search for:

```text
/trailing slash
```

Quit:

```text
q
```

## Questions

1. What does `rsync` mean?
    
2. What does `-a` mean?
    
3. What does `-v` mean?
    
4. What does `-n` mean?
    
5. What does `--delete` mean?
    
6. What is the purpose of a dry run?
    
7. Why must you be cautious with destination paths?
    

---

# Part 3: Run a Safe Dry Run

From the development machine:

```bash
cd ~/projects/site1
```

Run:

```bash
rsync -avn ./public/ deploy@app01:/var/www/site1/public/
```

Read the output carefully.

## Questions

1. Did any files actually change?
    
2. Which option prevented changes?
    
3. What files would be copied?
    
4. Does the output make sense?
    

## Important

`-n` means:

```text
show what would happen, but do not make changes
```

Do not remove `-n` until the output looks correct.

---

# Part 4: Deploy for Real

If the dry run output is correct:

```bash
rsync -av ./public/ deploy@app01:/var/www/site1/public/
```

Verify:

```bash
ssh deploy@app01
find /var/www/site1/public -type f -print
exit
```

Test the site:

```bash
curl -v http://app01/
```

Open in a browser:

```text
http://app01
```

## Questions

1. Did Nginx need to restart?
    
2. Why not?
    
3. What changed on the server?
    
4. How did `rsync` authenticate?
    

Correct idea:

```text
Nginx serves files dynamically from disk.
Static file updates do not require an Nginx restart.
```

---

# Part 5: Observe Incremental Copying

Modify only one local file:

```bash
vi ./public/index.html
```

Change:

```html
<h1>site1</h1>
```

to:

```html
<h1>site1 version 2</h1>
```

Dry run:

```bash
rsync -avn ./public/ deploy@app01:/var/www/site1/public/
```

Real deployment:

```bash
rsync -av ./public/ deploy@app01:/var/www/site1/public/
```

Test:

```bash
curl http://app01/
```

## Questions

1. Which file did `rsync` transfer?
    
2. Did it copy every file again?
    
3. Why is this useful for larger deployments?
    

Correct idea:

```text
rsync usually transfers only changed files.
```

---

# Part 6: Understand Stale Files

Create a temporary file locally:

```bash
echo "temporary page" > ./public/old-page.html
```

Deploy:

```bash
rsync -av ./public/ deploy@app01:/var/www/site1/public/
```

Verify:

```bash
curl http://app01/old-page.html
```

Now delete the local file:

```bash
rm ./public/old-page.html
```

Deploy again without `--delete`:

```bash
rsync -av ./public/ deploy@app01:/var/www/site1/public/
```

Check the server:

```bash
curl http://app01/old-page.html
```

## Questions

1. Does `old-page.html` still exist on the server?
    
2. Why?
    
3. How is this similar to the weakness of `scp`?
    
4. What problem does a stale file create?
    

Correct idea:

```text
Without --delete, rsync copies changes but does not remove remote files
that disappeared locally.
```

---

# Part 7: Use `--delete` Carefully

Before using `--delete`, run a dry run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/
```

Read the output.

You should see that `old-page.html` would be deleted.

If the output is correct:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

Verify:

```bash
curl -v http://app01/old-page.html
```

Expected result:

```text
404 Not Found
```

## Questions

1. What did `--delete` do?
    
2. Why should you always dry-run before using it?
    
3. What would happen if the destination path were wrong?
    
4. Should you use `--delete` casually?
    

Correct idea:

```text
--delete makes the destination mirror the source more closely.
A wrong destination path can remove unintended files.
```

---

# Part 8: Understand the Trailing Slash

This is important.

Compare:

```bash
rsync -av ./public/ deploy@app01:/var/www/site1/public/
```

with:

```bash
rsync -av ./public deploy@app01:/var/www/site1/
```

The first means:

```text
copy the contents of public/
into /var/www/site1/public/
```

The second means:

```text
copy the public directory itself
into /var/www/site1/
```

The result may look similar in this example, but the paths are not equivalent.

Create a safe experiment locally:

```bash
mkdir -p ~/rsync-lab/source
mkdir -p ~/rsync-lab/dest
echo "test" > ~/rsync-lab/source/file.txt
```

Run:

```bash
rsync -av ~/rsync-lab/source/ ~/rsync-lab/dest/
find ~/rsync-lab -type f -print
```

Reset:

```bash
rm -rf ~/rsync-lab/dest
mkdir ~/rsync-lab/dest
```

Run:

```bash
rsync -av ~/rsync-lab/source ~/rsync-lab/dest/
find ~/rsync-lab -type f -print
```

## Questions

1. How do the resulting paths differ?
    
2. Why does the trailing slash matter?
    
3. Why is this especially important when using `--delete`?
    

---

# Part 9: Add Compression Only After Understanding It

You may see examples using:

```bash
rsync -avz
```

The `-z` option enables compression during transfer.

For a small local home-lab site, compression is not especially important.

Use:

```bash
rsync -av
```

first.

Then compare:

```bash
rsync -avz ./public/ deploy@app01:/var/www/site1/public/
```

## Questions

1. What does `-z` do?
    
2. Is it necessary on a fast local network?
    
3. Why should you avoid adding options you do not understand?
    

---

# Part 10: Create a Deployment Script

Create:

```bash
vi deploy.sh
```

Enter:

```bash
#!/usr/bin/env bash
set -euo pipefail

LOCAL_DIR="./public/"
REMOTE_USER="deploy"
REMOTE_HOST="app01"
REMOTE_DIR="/var/www/site1/public/"

echo "Running dry run..."
rsync -avn --delete \
  "$LOCAL_DIR" \
  "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

echo
read -r -p "Deploy for real? [y/N] " answer

if [[ "$answer" == "y" || "$answer" == "Y" ]]; then
  rsync -av --delete \
    "$LOCAL_DIR" \
    "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"

  echo "Deployment complete."
else
  echo "Deployment cancelled."
fi
```

Make it executable:

```bash
chmod +x deploy.sh
```

Run:

```bash
./deploy.sh
```

## Questions

1. What does the shebang line do?
    
2. What does `set -euo pipefail` do?
    
3. Why are variables quoted?
    
4. Why is a dry run shown before deployment?
    
5. Why require confirmation?
    
6. Why is this better than manually typing the command every time?
    

---

# Part 11: Verify the Deployment Script

Modify the page:

```bash
vi ./public/index.html
```

Add:

```html
<p>Deployed using deploy.sh</p>
```

Run:

```bash
./deploy.sh
```

Verify:

```bash
curl http://app01/
```

Inspect remote files:

```bash
ssh deploy@app01
find /var/www/site1/public -type f -print
exit
```

## Questions

1. Did the script deploy the changed file?
    
2. Did it remove stale files?
    
3. Did it deploy only from `public/`?
    
4. Did the deployment require root access?
    

---

# Part 12: Break-and-Fix Exercises

## Exercise A: Wrong Destination Path

Do not run this for real.

Only dry-run:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/
```

Compare with:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/
```

## Questions

1. Are the destination paths the same?
    
2. What files might be affected by the first command?
    
3. Why is the second path safer?
    
4. Why must dry run come first?
    

---

## Exercise B: File Created Directly on Server

On `app01`:

```bash
ssh deploy@app01
echo "server-only file" > /var/www/site1/public/server-only.html
exit
```

Verify:

```bash
curl http://app01/server-only.html
```

Run dry-run deployment:

```bash
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/
```

Observe that the file would be removed.

Deploy:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

Verify:

```bash
curl -v http://app01/server-only.html
```

Expected:

```text
404 Not Found
```

## Lesson

The server is not the source of truth.

Manual edits on the server disappear during proper deployment.

---

## Exercise C: Broken Permissions After Deployment

On `app01`:

```bash
ssh deploy@app01
chmod 000 /var/www/site1/public/index.html
exit
```

Test:

```bash
curl -v http://app01/
```

Inspect logs:

```bash
ssh your-admin-user@app01
sudo tail -n 20 /var/log/nginx/site1.error.log
```

Fix by redeploying:

```bash
rsync -av --delete ./public/ deploy@app01:/var/www/site1/public/
```

If permissions remain wrong, inspect:

```bash
ssh deploy@app01
ls -l /var/www/site1/public/index.html
exit
```

Fix explicitly if needed:

```bash
ssh deploy@app01
chmod 644 /var/www/site1/public/index.html
exit
```

## Questions

1. Did `rsync` restore file contents?
    
2. Did it restore permissions?
    
3. What does archive mode preserve?
    
4. Which machine’s file permissions are being used as the source?
    

---

# Part 13: Compare `scp` and `rsync`

Fill in this table:

|Behavior|`scp`|`rsync`|
|---|---|---|
|Uses SSH|||
|Copies files|||
|Copies directories recursively|||
|Copies only changed files efficiently|||
|Supports dry run|||
|Can remove stale destination files|||
|Good for one-time simple copy|||
|Good for repeatable deployment|||

Expected summary:

```text
scp is good for learning basic secure copying.
rsync is better for repeatable deployment.
```

---

# Part 14: Lab Journal

Record:

```text
Command:
What I expected:
What changed:
How I verified it:
How I would undo it:
```

Example:

```text
Command:
rsync -avn --delete ./public/ deploy@app01:/var/www/site1/public/

What I expected:
Preview changes without modifying the server.

What changed:
Nothing. It was a dry run.

How I verified it:
Checked the server files before and after.

How I would undo it:
No undo needed because dry run made no changes.
```

---

# Completion Checkpoint

Before moving on, explain:

1. What does `rsync` do?
    
2. How is `rsync` different from `scp`?
    
3. What does `-a` mean?
    
4. What does `-v` mean?
    
5. What does `-n` mean?
    
6. What does `--delete` mean?
    
7. Why is `--delete` dangerous?
    
8. Why should a dry run come first?
    
9. Why does the trailing slash matter?
    
10. Why deploy only from `public/`?
    
11. Why should the server not be edited manually?
    
12. What happens to a server-only file during deployment with `--delete`?
    
13. Why does Nginx not need to restart after a static-file update?
    
14. Why should normal deployment not require `sudo`?
    
15. Why is a script better than manually typing a long command repeatedly?
    

## What this phase should accomplish

The important transition is:

```text
Phase 1:
copy files manually

Phase 2:
deploy an intentional directory state repeatably
```

Do not introduce GitHub Actions yet.

The next sensible phase is:

```text
local Git repository
→ commit changes
→ run deploy.sh
→ verify deployment
```

That teaches source control and deployment as separate concepts.

[source](https://chatgpt.com/s/t_6a21e59363888191b8aff6944ae8d6a2)
