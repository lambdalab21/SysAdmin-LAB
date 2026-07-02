
```text
copy these files from this machine to that machine over SSH
```

It teaches the basic client/server file-transfer model. Later, introduce `rsync` as an improvement when he notices the weaknesses of repeated copying.

`scp` uses SSH for the transfer and authentication. The `-r` option copies directories recursively; it also follows symbolic links encountered during traversal, which is worth knowing. ([man.openbsd.org](https://man.openbsd.org/scp.1?utm_source=chatgpt.com "scp(1) - OpenBSD manual pages"))

One important limitation:

> `scp` copies files, but it does not synchronize directories.

If he deletes a file locally and redeploys with `scp`, the old file may remain on the server. That imperfection creates a useful reason to learn `rsync` later.

# Phase 1 Lab: Deploy a Static Website to Nginx with `scp`

## Goal

Configure `app01`, an AlmaLinux server, to serve a static website named `site1` using Nginx.

Create the website on the development machine and deploy it to `app01` using `scp` over SSH.

## Rules

1. Do not log in as `root`.
    
2. Do not deploy files as `root`.
    
3. Use `sudo` only when changing system configuration.
    
4. Create and edit the website on the development machine.
    
5. Copy the website files to `app01` using `scp`.
    
6. Do not edit the deployed website files directly on `app01`.
    
7. Verify every step using commands.
    
8. Use `man` pages to inspect unfamiliar options before running commands.
    

---

# Part 1: Install Nginx on `app01`

Log in to the AlmaLinux server:

```bash
ssh your-admin-user@app01
```

Install Nginx:

```bash
sudo dnf install nginx
```

Verify that the package is installed:

```bash
rpm -q nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable Nginx so it starts automatically after reboot:

```bash
sudo systemctl enable nginx
```

Check its status:

```bash
systemctl status nginx
```

Check whether Nginx is listening on TCP port 80:

```bash
sudo ss -tulpn | grep ':80'
```

## Questions

1. What did `dnf install nginx` change?
    
2. What is a package manager?
    
3. What is the difference between these commands?
    

```bash
systemctl start nginx
systemctl enable nginx
```

4. What does TCP port `80` represent?
    
5. What does `ss -tulpn` show?
    
6. If Nginx is running but not enabled, what happens after reboot?
    

---

# Part 2: Allow HTTP Through the Firewall

Check the current firewall configuration:

```bash
sudo firewall-cmd --list-all
```

Allow HTTP traffic permanently:

```bash
sudo firewall-cmd --add-service=http --permanent
```

Reload the firewall configuration:

```bash
sudo firewall-cmd --reload
```

Verify the rule:

```bash
sudo firewall-cmd --list-all
```

From another machine, test the default Nginx page:

```bash
curl -v http://app01
```

Use the server IP address instead if the hostname does not resolve:

```bash
curl -v http://192.168.x.x
```

## Questions

1. What is the difference between Nginx listening on port 80 and the firewall allowing HTTP traffic?
    
2. What does `--permanent` mean?
    
3. Why is `firewall-cmd --reload` needed?
    
4. What information does `curl -v` show?
    
5. Can a service be running locally but unreachable from another machine?
    

---

# Part 3: Create a Deployment User

If a suitable non-root deployment user does not already exist, create one:

```bash
sudo useradd -m deploy
sudo passwd deploy
```

Verify the account:

```bash
id deploy
getent passwd deploy
```

The deployment user should be able to log in through SSH:

```bash
ssh deploy@app01
```

## Questions

1. What did `useradd -m deploy` create?
    
2. What is the purpose of the home directory?
    
3. Where is the user account recorded?
    
4. Why should routine deployments not require `root`?
    

---

# Part 4: Create the Website Directory

On `app01`, check which user Nginx uses:

```bash
grep '^user' /etc/nginx/nginx.conf
```

On AlmaLinux, the result will usually be:

```text
user nginx;
```

Create the website directory:

```bash
sudo mkdir -p /var/www/site1/public
```

Give the deployment user ownership and the Nginx group read access:

```bash
sudo chown -R deploy:nginx /var/www/site1
sudo chmod -R 2755 /var/www/site1
```

Inspect the result:

```bash
ls -ld /var/www/site1
ls -ld /var/www/site1/public
```

## Questions

1. Why are the website files stored under `/var/www`?
    
2. Why is there a `public` directory?
    
3. What does this mean?
    

```text
deploy:nginx
```

4. Why does `deploy` need write permission?
    
5. Why should Nginx need read permission but not write permission?
    
6. What does `2755` mean?
    
7. What is the setgid bit on a directory?
    

---

# Part 5: Configure Nginx for `site1`

Create a configuration file:

```bash
sudo vi /etc/nginx/conf.d/site1.conf
```

Enter:

```nginx
server {
    listen 80;
    server_name site1.local app01;

    root /var/www/site1/public;
    index index.html;

    access_log /var/log/nginx/site1.access.log;
    error_log  /var/log/nginx/site1.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Test the configuration:

```bash
sudo nginx -t
```

If the configuration test succeeds, reload Nginx:

```bash
sudo systemctl reload nginx
```

Check status:

```bash
systemctl status nginx
```

## Questions

1. What does `listen 80` mean?
    
2. What does `server_name` do?
    
3. What does `root` mean in this configuration?
    
4. What is the purpose of `index index.html`?
    
5. Why should you run `nginx -t` before reloading Nginx?
    
6. What is the difference between `reload` and `restart`?
    

---

# Part 6: Create the Website on the Development Machine

On the development machine:

```bash
mkdir -p ~/projects/site1/public
cd ~/projects/site1
```

Create:

```bash
vi public/index.html
```

Enter:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>site1</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>site1</h1>
  <p>Deployed with scp to app01.</p>
</body>
</html>
```

Create:

```bash
vi public/style.css
```

Enter:

```css
body {
  font-family: sans-serif;
  max-width: 700px;
  margin: 3rem auto;
  line-height: 1.5;
}
```

Inspect the local files:

```bash
find ./public -type f -print
```

## Questions

1. Why should only publicly accessible files go inside `public/`?
    
2. Should passwords, notes, `.env` files, or private keys go inside `public/`?
    
3. Why should he edit files on the development machine instead of editing files directly on the server?
    

---

# Part 7: Read the `scp` Manual Page

Before copying files, inspect the manual page:

```bash
man scp
```

Search for the recursive option:

```text
/-r
```

Press `n` to move to the next match.

Quit:

```text
q
```

## Questions

1. What does `scp` stand for?
    
2. How does `scp` authenticate with the remote server?
    
3. What does `-r` mean?
    
4. What does this colon mean?
    

```text
deploy@app01:/var/www/site1/public/
```

5. Which part of the command is local?
    
6. Which part is remote?
    

---

# Part 8: Deploy One File First

From the development machine:

```bash
cd ~/projects/site1
```

Copy only the HTML file:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

Verify remotely:

```bash
ssh deploy@app01
ls -l /var/www/site1/public
exit
```

Test from the development machine:

```bash
curl -v http://app01
```

## Questions

1. What file moved across the network?
    
2. Which protocol protected the transfer?
    
3. Why could `deploy` write into `/var/www/site1/public/` without using `sudo`?
    
4. Why could Nginx read the file?
    

---

# Part 9: Deploy the Remaining Files

Copy the CSS file:

```bash
scp ./public/style.css deploy@app01:/var/www/site1/public/
```

Test again:

```bash
curl -v http://app01
```

Open the website in a browser:

```text
http://app01
```

## Questions

1. What changed after copying `style.css`?
    
2. Did Nginx need to restart?
    
3. Why or why not?
    

---

# Part 10: Deploy the Entire `public` Directory

Create another file locally:

```bash
vi ./public/about.html
```

Enter:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>About site1</title>
</head>
<body>
  <h1>About site1</h1>
  <p>This page was copied recursively with scp.</p>
</body>
</html>
```

Read the manual page again:

```bash
man scp
```

Search for:

```text
/-r
```

Deploy the contents of the local `public` directory:

```bash
scp -r ./public/. deploy@app01:/var/www/site1/public/
```

Inspect the deployed files:

```bash
ssh deploy@app01
find /var/www/site1/public -type f -print
exit
```

Test:

```bash
curl -v http://app01/about.html
```

## Questions

1. What does `-r` do?
    
2. Why is the source path written as `./public/.`?
    
3. What files were copied?
    
4. Does `scp` remove old files from the server if they no longer exist locally?
    
5. What could go wrong if a symbolic link exists inside the copied directory?
    

---

# Part 11: Inspect Logs

On `app01`, watch the access log:

```bash
sudo tail -f /var/log/nginx/site1.access.log
```

From another terminal, request pages:

```bash
curl http://app01
curl http://app01/about.html
curl http://app01/missing-page.html
```

Inspect the error log:

```bash
sudo tail -n 50 /var/log/nginx/site1.error.log
```

Inspect the systemd journal:

```bash
journalctl -u nginx --since "10 minutes ago"
```

## Questions

1. What appears in the access log?
    
2. What HTTP status code is returned for a missing page?
    
3. What appears in the error log?
    
4. How is `journalctl` different from the Nginx access log?
    

---

# Part 12: Break and Fix the Deployment

## Exercise A: Wrong Permission

On `app01`:

```bash
chmod 000 /var/www/site1/public/index.html
```

From another machine:

```bash
curl -v http://app01
```

Inspect logs:

```bash
sudo tail -n 20 /var/log/nginx/site1.error.log
```

Fix the permission:

```bash
chmod 644 /var/www/site1/public/index.html
```

Test again:

```bash
curl -v http://app01
```

### Lesson

Nginx must be able to read the website file.

---

## Exercise B: Firewall Block

On `app01`:

```bash
sudo firewall-cmd --remove-service=http --permanent
sudo firewall-cmd --reload
```

Test remotely:

```bash
curl -v http://app01
```

Restore access:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

Test again:

```bash
curl -v http://app01
```

### Lesson

A running service is not necessarily reachable from another machine.

---

## Exercise C: Bad Nginx Configuration

Add a deliberate typo to:

```text
/etc/nginx/conf.d/site1.conf
```

Test:

```bash
sudo nginx -t
```

Do not reload Nginx while the configuration test fails.

Fix the typo:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Lesson

Validate configuration before applying it.

---

# Part 13: Observe the Weakness of `scp`

On the development machine, delete:

```bash
rm ./public/about.html
```

Redeploy:

```bash
scp -r ./public/. deploy@app01:/var/www/site1/public/
```

Check the server:

```bash
ssh deploy@app01
ls -l /var/www/site1/public
exit
```

## Questions

1. Does `about.html` still exist on the server?
    
2. Why?
    
3. How could stale files become a problem?
    
4. What capability should the next deployment tool provide?
    

### Lesson

`scp` copies files. It does not make the destination directory exactly match the source directory.

Later, use `rsync` to solve this problem.

---

# Lab Journal

For each important command, record:

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
scp ./public/index.html deploy@app01:/var/www/site1/public/

What I expected:
Copy index.html securely from the development machine to app01.

What changed:
A file appeared at /var/www/site1/public/index.html on app01.

How I verified it:
ssh deploy@app01
ls -l /var/www/site1/public

How I would undo it:
ssh deploy@app01
rm /var/www/site1/public/index.html
```

---

# Completion Checkpoint

Before moving to `rsync`, explain:

1. What does Nginx do?
    
2. What is a Linux service?
    
3. What is TCP port 80?
    
4. How do you prove that Nginx is running?
    
5. How do you prove that Nginx is listening?
    
6. How do you prove that the firewall allows HTTP?
    
7. Why is the site under `/var/www/site1/public/`?
    
8. Which user writes deployed files?
    
9. Which user reads deployed files?
    
10. Why does normal deployment not require `sudo`?
    
11. What does `scp` do?
    
12. How does `scp` use SSH?
    
13. What does `scp -r` do?
    
14. What does the colon mean in an `scp` destination?
    
15. How is copying one file different from copying a directory?
    
16. Where are Nginx access logs?
    
17. Where are Nginx error logs?
    
18. Why is `nginx -t` important?
    
19. What is one major limitation of `scp`?
    
20. Why might `rsync` be useful for the next phase?
    

## Why `scp` is better for Phase 1

With `scp`, he sees the primitive operation clearly:

```text
local file → SSH connection → remote directory
```

Then he discovers the inconvenience:

```text
deleted local file ≠ deleted remote file
```

That gives him a real reason to learn `rsync`, instead of treating it as another command to memorize.

## One simplification

For the first session, he does not need to complete every break/fix exercise. The minimum useful path is:

1. install and start Nginx
    
2. open the firewall
    
3. create `/var/www/site1/public/`
    
4. configure the Nginx server block
    
5. copy `index.html` with `scp`
    
6. inspect the site with `curl`
    
7. inspect the logs
    
8. delete a local file and observe that `scp` does not clean the server
    

Then move to `rsync` in Phase 2.

