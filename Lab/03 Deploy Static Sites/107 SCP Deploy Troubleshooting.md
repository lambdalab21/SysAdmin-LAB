#LAB #deployment
#ssh
The command is reasonable:

```bash
scp -r ./dist/. deploy@app01:/var/www/site1/public/
```

The likely problem is **server-side directory ownership/permissions**, not `scp` itself.

## First: identify which permission error

He should not guess. Read the exact error.

Common examples:

```text
scp: dest open "/var/www/site1/public/index.html": Permission denied
```

means `deploy` can connect, but cannot write to the destination.

```text
Permission denied (publickey,password)
```

means SSH login failed.

Those are different problems.

## Step 1: Can `deploy` SSH into app01?

From the development machine:

```bash
ssh deploy@app01
```

If this fails, fix SSH first. Do not troubleshoot `/var/www` yet.

On success, run:

```bash
whoami
id
pwd
```

Expected:

```text
whoami → deploy
```

## Step 2: Does the destination directory exist?

On `app01`:

```bash
ls -ld /var/www
ls -ld /var/www/site1
ls -ld /var/www/site1/public
```

If `/var/www/site1/public` does not exist:

```bash
sudo mkdir -p /var/www/site1/public
```

## Step 3: Check ownership

On `app01`:

```bash
ls -ld /var/www/site1/public
```

Bad example:

```text
drwxr-xr-x. 2 root root 4096 ... /var/www/site1/public
```

That means only `root` can write there. `deploy` cannot.

Better:

```text
drwxrwsr-x. 2 deploy nginx 4096 ... /var/www/site1/public
```

or at least:

```text
drwxr-xr-x. 2 deploy nginx 4096 ... /var/www/site1/public
```

Fix:

```bash
sudo chown -R deploy:nginx /var/www/site1
sudo chmod -R 2755 /var/www/site1
```

Then verify:

```bash
ls -ld /var/www/site1 /var/www/site1/public
```

## Step 4: Confirm the Nginx group name

On AlmaLinux, Nginx usually runs as `nginx`, but verify:

```bash
grep '^user' /etc/nginx/nginx.conf
```

Likely output:

```text
user nginx;
```

If it says a different user, use that group/user accordingly.

Also check:

```bash
ps aux | grep nginx
```

## Step 5: Test whether `deploy` can write manually

On `app01`, as `deploy`:

```bash
touch /var/www/site1/public/test.txt
ls -l /var/www/site1/public/test.txt
rm /var/www/site1/public/test.txt
```

If `touch` fails, `scp` will fail too. Fix ownership/permissions before retrying.

## Step 6: Check parent directory execute permissions

A common trap: the file directory may look fine, but a parent directory blocks traversal.

Run:

```bash
namei -l /var/www/site1/public
```

He should see execute permission `x` on every parent directory for the relevant user/group/other.

Example:

```text
f: /var/www/site1/public
dr-xr-xr-x root root /
drwxr-xr-x root root var
drwxr-xr-x root root www
drwxrwsr-x deploy nginx site1
drwxrwsr-x deploy nginx public
```

If any parent directory lacks `x`, users cannot pass through it.

## Step 7: Try the `scp` command again

From the development machine:

```bash
scp -r ./dist/. deploy@app01:/var/www/site1/public/
```

This copies the contents of `dist/`, not the `dist` directory itself.

That part is correct.

## Step 8: Prefer `rsync` after this test

`scp` is okay for a first test, but `rsync` is better for deployment:

```bash
rsync -avzn --delete ./dist/ deploy@app01:/var/www/site1/public/
```

Then real run:

```bash
rsync -avz --delete ./dist/ deploy@app01:/var/www/site1/public/
```

The trailing slash matters:

```bash
./dist/
```

means “copy the contents of `dist`.”

## Step 9: If permissions still look correct, check SELinux

AlmaLinux may have SELinux enforcing. First check:

```bash
getenforce
```

If it says:

```text
Enforcing
```

then after copying files, Nginx may fail to serve them even though `scp` succeeded. That is a different issue: **Nginx read/access denied**, not **scp write denied**.

Check file contexts:

```bash
ls -Z /var/www/site1/public
```

For web content, a common fix is:

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/site1/public(/.*)?"
sudo restorecon -Rv /var/www/site1/public
```

If `semanage` is missing:

```bash
sudo dnf install policycoreutils-python-utils
```

But do not jump to SELinux first. If `scp` itself says permission denied while writing, Unix ownership/permissions are the first suspect.

## Clean setup command set

On `app01`, this is a reasonable baseline:

```bash
sudo mkdir -p /var/www/site1/public
sudo chown -R deploy:nginx /var/www/site1
sudo chmod -R 2755 /var/www/site1
```

Test as deploy:

```bash
touch /var/www/site1/public/test.txt
rm /var/www/site1/public/test.txt
```

Then from dev machine:

```bash
scp -r ./dist/. deploy@app01:/var/www/site1/public/
```

## What he should learn from this

The key lesson:

```text
SSH login permission ≠ filesystem write permission ≠ Nginx read permission ≠ SELinux permission
```

They are separate gates.

He should troubleshoot in this order:

1. Can I SSH as `deploy`?
    
2. Does the destination directory exist?
    
3. Who owns it?
    
4. Can `deploy` write there manually?
    
5. Can parent directories be traversed?
    
6. Did the copy succeed?
    
7. Can Nginx read and serve the files?
    
8. Is SELinux blocking Nginx?
    

That is infrastructure thinking. Don’t let him randomly `chmod 777` the directory. That “fixes” the symptom by destroying the lesson.


