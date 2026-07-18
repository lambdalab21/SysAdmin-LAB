#LAB #deployment
#ssh

This phase is not “install Nginx and copy files.” That is too shallow.


This phase should teach:

> A web server is a Linux service that reads files from a controlled directory, listens on a port, is allowed through a firewall, writes logs, and can be updated through a repeatable deployment command.

## Phase 1 learning goals

By the end, he should understand these ideas:

|Area|What he should learn|
|---|---|
|Package management|Installing Nginx with `dnf`; checking installed package info.|
|systemd|Starting, enabling, stopping, reloading, and checking Nginx.|
|Ports|Nginx listens on TCP port 80.|
|Firewall|A service can be running but unreachable if the firewall blocks it.|
|Web root|Nginx serves files from a configured directory.|
|Ownership|Deployment user writes files; Nginx only reads them.|
|Permissions|Linux permissions control whether Nginx can read files.|
|Logs|Access logs show requests; error logs show problems.|
|Deployment|`rsync` makes the server match the local deployment directory.|
|Verification|Never assume it worked. Test with `curl`, browser, logs, `ss`, and `systemctl`.|

## Phase 1 assignment

Give him this exact assignment.

```text
Goal:
Configure app01, an AlmaLinux server, to serve a static website named site1 using Nginx. 
Deploy the site from the development machine using rsync over SSH.

Rules:
1. Do not deploy as root.
2. Do not edit website files directly on app01 after the initial setup.
3. Use rsync from the development machine.
4. Use a dry run before the real rsync deployment.
5. Verify each step with commands, not feelings.
6. Write down what each command changed on the machine.
```

## Step 0: Define assumptions

Assume:

```text
Server: app01
Server OS: AlmaLinux
Deployment user: deploy
Site name: site1
Server web directory: /var/www/site1/public
Local development directory: ~/projects/site1/public
```

If the deployment user is not named `deploy`, replace it with the correct username.

## Step 1: Install Nginx on app01

On `app01`:

```bash
sudo dnf install nginx
```

Check package:

```bash
rpm -q nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable it at boot:

```bash
sudo systemctl enable nginx
```

Check status:

```bash
systemctl status nginx
```

Check whether it is listening:

```bash
ss -tulpn | grep ':80'
```

### What he should learn

He should explain:

- What `dnf install nginx` did.
    
- What a package manager does.
    
- Difference between `systemctl start nginx` and `systemctl enable nginx`.
    
- What port 80 means.
    
- Why `ss -tulpn` is useful.
    

Checkpoint question:

> If `nginx` is running but not enabled, what happens after reboot?

Correct idea:

> It runs now, but may not start automatically after reboot.

## Step 2: Open firewall for HTTP

On app01:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

Test from another machine:

```bash
curl -v http://app01
```

Or use the server IP:

```bash
curl -v http://192.168.x.x
```

### What he should learn

He should explain:

- What `firewalld` is doing.
    
- Difference between Nginx listening and the firewall allowing access.
    
- Why `--permanent` matters.
    
- Why `firewall-cmd --reload` is needed.
    
- What `curl -v` shows.
    

Checkpoint question:

> If Nginx is running and listening on port 80, but the firewall blocks HTTP, what happens from another machine?

Correct idea:

> The service exists locally, but remote clients cannot reach it.

## Step 3: Create the site directory

On app01:

```bash
sudo mkdir -p /var/www/site1/public
```

Set ownership. First find the Nginx user:

```bash
grep '^user' /etc/nginx/nginx.conf
```

On AlmaLinux it is usually:

```text
user nginx;
```

Set ownership:

```bash
sudo chown -R deploy:nginx /var/www/site1
sudo chmod -R 2755 /var/www/site1
```

Check:

```bash
ls -ld /var/www/site1
ls -ld /var/www/site1/public
```

### What he should learn

He should explain:

- Why the site is under `/var/www`.
    
- Why there is a `public` directory.
    
- Why Nginx should not need write permission.
    
- Why the deployment user needs write permission.
    
- What `deploy:nginx` means.
    
- What `2755` means.
    
- What the setgid bit does on a directory.
    

Important idea:

```text
deploy = writes website files
nginx  = reads website files
```

Checkpoint question:

> Why not just make everything owned by root and use sudo every time?

Correct idea:

> Because deployment should not require root for normal file updates. Root should be used for system configuration, not routine publishing.

## Step 4: Create Nginx config for site1

On app01, create:

```bash
sudo vim /etc/nginx/conf.d/site1.conf
```

Put:

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

Test config:

```bash
sudo nginx -t
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

### What he should learn

He should explain:

- What `server {}` means.
    
- What `listen 80` means.
    
- What `server_name` does.
    
- What `root` does.
    
- What `index index.html` does.
    
- What `try_files $uri $uri/ =404` means.
    
- Why `nginx -t` comes before reload.
    
- Difference between reload and restart.
    

Checkpoint question:

> If the Nginx config has a typo and he runs `systemctl reload nginx`, what might happen?

Correct idea:

> Nginx may reject the config, or the service may fail depending on the situation. Always run `nginx -t` first.

## Step 5: Create local site on development machine

On his development machine:

```bash
mkdir -p ~/projects/site1/public
cd ~/projects/site1
```

Create:

```bash
vi public/index.html
```

Example:

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
  <p>Deployed with rsync to app01.</p>
</body>
</html>
```

Create:

```bash
vi public/style.css
```

Example:

```css
body {
  font-family: sans-serif;
  max-width: 700px;
  margin: 3rem auto;
  line-height: 1.5;
}
```

### What he should learn

He should understand:

- `public/` contains only files safe to serve.
    
- Notes, scripts, `.git`, and secrets should not go into the public web root.
    
- The development machine is where he edits.
    
- The server is where he deploys.
    

## Step 6: Deploy with rsync dry run

From the development machine:

```bash
rsync -avzn --delete ./public/ deploy@app01:/var/www/site1/public/
```

Do not run the real command until he reads the output.

### What he should learn

He should explain:

- `-a`
    
- `-v`
    
- `-z`
    
- `-n`
    
- `--delete`
    
- source path
    
- destination path
    
- trailing slash after `./public/`
    

The dangerous part is this:

```bash
--delete
```

It means files on the server destination that are not in local `public/` may be deleted.

Checkpoint question:

> Why is dry run important before using `--delete`?

Correct idea:

> Because a wrong destination path plus `--delete` can remove the wrong files.

## Step 7: Deploy for real

If the dry run looks correct:

```bash
rsync -avz --delete ./public/ deploy@app01:/var/www/site1/public/
```

Then test:

```bash
curl -v http://app01
```

Or:

```bash
curl -H "Host: site1.local" http://app01
```

From browser:

```text
http://app01
```

or:

```text
http://site1.local
```

If using `site1.local`, add this to the client machine’s `/etc/hosts`:

```text
192.168.x.x site1.local
```

## Step 8: Check logs

On app01:

```bash
sudo tail -f /var/log/nginx/site1.access.log
```

In another terminal, request:

```bash
curl http://app01
```

Check error log:

```bash
sudo tail -n 50 /var/log/nginx/site1.error.log
```

Check journal:

```bash
journalctl -u nginx --since "10 minutes ago"
```

### What he should learn

He should understand:

- Access log = requests received.
    
- Error log = Nginx problems.
    
- `journalctl` = service-level logs.
    
- Logs are evidence, not decoration.
    

## Step 9: Break/fix exercises

This is where learning happens.

### Break 1: Wrong file permission

On app01:

```bash
chmod 000 /var/www/site1/public/index.html
```

Test:

```bash
curl -v http://app01
```

Check logs.

Fix:

```bash
chmod 644 /var/www/site1/public/index.html
```

Lesson:

> Nginx must be able to read the file.

### Break 2: Missing index file

On development machine:

```bash
mv public/index.html public/home.html
rsync -avzn --delete ./public/ deploy@app01:/var/www/site1/public/
rsync -avz --delete ./public/ deploy@app01:/var/www/site1/public/
```

Test.

Fix it.

Lesson:

> `index index.html` matters.

### Break 3: Firewall block

On app01:

```bash
sudo firewall-cmd --remove-service=http --permanent
sudo firewall-cmd --reload
```

Test from another machine.

Fix:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

Lesson:

> Running service does not mean reachable service.

### Break 4: Bad Nginx config

Add a typo in `/etc/nginx/conf.d/site1.conf`.

Then:

```bash
sudo nginx -t
```

Do **not** reload until the test passes.

Lesson:

> Validate config before touching a running service.

## What he should write down

Have him keep a small lab journal.

For every command:

```text
Command:
What it changed:
How I verified it:
How I would undo it:
```

Example:

```text
Command:
sudo firewall-cmd --add-service=http --permanent

What it changed:
It added a permanent firewalld rule allowing HTTP traffic.

How I verified it:
sudo firewall-cmd --list-all

How I would undo it:
sudo firewall-cmd --remove-service=http --permanent
sudo firewall-cmd --reload
```

## What not to teach yet

Do not add these yet:

- GitHub Actions
    
- Docker
    
- HTTPS
    
- reverse proxy
    
- PostgreSQL
    
- app frameworks
    
- public internet exposure
    
- SELinux deep dive
    

SELinux may interfere on AlmaLinux, so he may encounter it. But do not make SELinux the main lesson yet. If it appears, treat it as a controlled side lesson.

## When this phase is complete

He is done with Phase 1 only when he can answer:

1. What does Nginx do?
    
2. What port does HTTP use?
    
3. How do you know Nginx is running?
    
4. How do you know Nginx is listening?
    
5. How do you know the firewall allows HTTP?
    
6. Why is `/var/www/site1/public` used?
    
7. Who owns the site files?
    
8. Which user does Nginx run as?
    
9. Why should Nginx not write to the site directory?
    
10. What does `root` mean in Nginx config?
    
11. What does `server_name` mean?
    
12. Why run `nginx -t` before reload?
    
13. What is the difference between reload and restart?
    
14. What does `rsync --delete` do?
    
15. Why use dry run first?
    
16. Where are Nginx access logs?
    
17. Where are Nginx error logs?
    
18. How can he prove the deployment worked?
    

## Next step after Phase 1

After he completes this, the next phase should be:

> Same static site, but with Git-controlled source and a `deploy.sh` script.

Then later:

> Nginx reverse proxy to a small backend app running as a systemd service.

Do not rush. Static site deployment is enough if he actually understands the chain:

```text
local files → rsync over SSH → Linux permissions → Nginx config → firewall → HTTP request → logs
```

That chain is real infrastructure.