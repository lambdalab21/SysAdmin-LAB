
 **Static sites are the right next step.** In fact, they are better than a dynamic app right now.

A static site forces him to learn the infrastructure layers without hiding behind an application framework:

- where web files live
    
- who owns them
    
- how Nginx finds them
    
- how Linux permissions affect serving files
    
- how firewall rules affect access
    
- how logs show requests and errors
    
- how to deploy changes safely
    
- how to avoid “it works because I randomly copied files as root”
    


## What he should build first

On AlmaLinux machine `app01`, have him serve **three separate static sites**:

```text
/var/www/site1
/var/www/site2
/var/www/site3
```

Example:

```text
site1.local
site2.local
site3.local
```

Since this is home lab, he can fake DNS by editing `/etc/hosts` on the client machine:

```text
192.168.1.50 site1.local
192.168.1.50 site2.local
192.168.1.50 site3.local
```

Then Nginx can use **virtual hosts** / **server blocks**.

This is a very useful concept. One server, one IP address, multiple websites.

## How should he deploy?

Do **not** use FTP.

FTP is old, insecure by default, and teaches the wrong habits unless the lesson is “why not FTP.” Skip it.

Best progression:

## Phase 1: Manual deployment with `scp` or `rsync`

Start here.

From his development machine:

```bash
rsync -av --delete ./site1/ deploy@app01:/var/www/site1/
```

This is better than USB because:

- it uses SSH
    
- it is repeatable
    
- it teaches remote deployment
    
- it avoids physically touching the server
    
- `--delete` teaches that deployment must match source state
    

But do not let him blindly use `--delete` without understanding it. Make him first run:

```bash
rsync -avn --delete ./site1/ deploy@app01:/var/www/site1/
```

`-n` is dry run.

He should explain:

- What does `-a` do?
    
- What does `-v` do?
    
- What does `-n` do?
    
- What does `--delete` do?
    
- Why is the trailing slash in `./site1/` important?
    

That last one matters.


## Phase 2: Git on the development machine, deploy with `rsync`

This is probably the best early workflow.

He edits locally:

```bash
git init
git add .
git commit -m "Create first static site"
```

Then deploys with:

```bash
rsync -av --delete ./ deploy@app01:/var/www/site1/
```

This teaches a clean split:

- **Git** = source control
    
- **rsync over SSH** = deployment
    

That is a good mental model.

## Phase 3: GitHub remote, still deploy with `rsync`

Later:

```bash
git remote add origin git@github.com:username/site1.git
git push -u origin main
```

But GitHub should not be the first deployment mechanism. GitHub is for source history and collaboration. It is not automatically deployment unless he deliberately builds CI/CD later.

## Phase 4: Pull from Git on the server

This is useful, but I would delay it.

The server can do:

```bash
cd /var/www/site1
git pull
```

But this has problems:

- now the server needs GitHub credentials or deploy keys
    
- `.git` may live under `/var/www`
    
- permissions get messy
    
- he may start editing on the server
    
- it blurs source-control and deployment concepts
    

For a beginner, **local Git + rsync deploy** is cleaner.

## Phase 5: GitHub Actions / CI/CD

Later, after he understands manual deployment.

Eventually:

```text
git push → GitHub Actions → SSH/rsync → app01
```

That is a good final goal, but not now. If he starts there, he will paste YAML and understand nothing.

## USB drive?

Use USB only as a comparison exercise.

It is not wrong, but it is not realistic deployment. It teaches file copying, not infrastructure.

A useful lesson:

1. Copy site with USB.
    
2. Copy site with `scp`.
    
3. Copy site with `rsync`.
    
4. Compare which one is repeatable and safest.
    

He will learn why infrastructure people prefer scripted remote deployment.

## Recommended user setup

Do **not** deploy as root.

Create a deployment user:

```bash
sudo useradd -m deploy
sudo passwd deploy
```

Set up SSH key login for `deploy`.

Then decide ownership.

I recommend:

```bash
sudo mkdir -p /var/www/site1
sudo chown -R deploy:nginx /var/www/site1
sudo chmod -R 2755 /var/www/site1
```

On AlmaLinux, the Nginx worker user is often `nginx`.

Check:

```bash
ps aux | grep nginx
```

or:

```bash
grep '^user' /etc/nginx/nginx.conf
```

He should understand this:

- `deploy` can write files.
    
- `nginx` can read files.
    
- random users cannot modify the website.
    
- Nginx should not need write access to static files.
    

This is a serious lesson.

## Better directory layout

Instead of dumping everything loosely into `/var/www`, use:

```text
/var/www/
  site1/
    public/
      index.html
      style.css
  site2/
    public/
      index.html
  site3/
    public/
      index.html
```

Then Nginx points to:

```text
/var/www/site1/public
```

Why `public/`?

Because later, if he has app config, scripts, source files, or notes, they should not be web-accessible.

Even for static sites, this teaches good structure.

## Nginx config layout

On AlmaLinux, use something like:

```text
/etc/nginx/conf.d/site1.conf
```

Example:

```nginx
server {
    listen 80;
    server_name site1.local;

    root /var/www/site1/public;
    index index.html;

    access_log /var/log/nginx/site1.access.log;
    error_log  /var/log/nginx/site1.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Then test config:

```bash
sudo nginx -t
```

Reload, not restart:

```bash
sudo systemctl reload nginx
```

He should know the difference:

- `restart` stops and starts the service.
    
- `reload` asks Nginx to re-read config without fully stopping.
    

For Nginx config changes, usually use reload after `nginx -t`.

## Firewall

Allow HTTP:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

Then test from another machine:

```bash
curl -v http://site1.local
```

or:

```bash
curl -H "Host: site1.local" http://app01
```

That second command is excellent because it shows how virtual hosts depend on the `Host` header.

## Logs he should inspect

Access log:

```bash
sudo tail -f /var/log/nginx/site1.access.log
```

Error log:

```bash
sudo tail -f /var/log/nginx/site1.error.log
```

Systemd status:

```bash
systemctl status nginx
```

Journal:

```bash
journalctl -u nginx --since "10 minutes ago"
```

He should intentionally break something:

- wrong root directory
    
- wrong permission
    
- missing index.html
    
- typo in Nginx config
    
- blocked firewall
    
- wrong `/etc/hosts`
    

Then diagnose from:

```bash
nginx -t
systemctl status nginx
journalctl -u nginx
/var/log/nginx/*.log
ss -tulpn
firewall-cmd --list-all
curl -v
```

That is real learning.

## What the first deployment script should look like

Make a local script named:

```text
deploy.sh
```

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

SITE_DIR="./public/"
REMOTE_USER="deploy"
REMOTE_HOST="app01"
REMOTE_DIR="/var/www/site1/public/"

rsync -av --delete "$SITE_DIR" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"
```

Then:

```bash
chmod +x deploy.sh
./deploy.sh
```

Later add dry-run mode.

He should understand every line:

- `#!/usr/bin/env bash`
    
- `set -euo pipefail`
    
- variables
    
- quoted variables
    
- `rsync`
    
- source and destination paths
    

This connects directly to LCL redirection/quoting/shell scripting.

## What not to do

Do not start with:

- FTP
    
- GUI file manager copy
    
- root SSH login
    
- editing files directly in `/var/www` with `sudo vim`
    
- GitHub Actions immediately
    
- Docker immediately
    
- public internet exposure immediately
    
- HTTPS immediately
    

HTTPS is important, but first he should understand plain HTTP, Nginx, firewall, logs, and deployment. Then add TLS later with a local CA or real domain.

## Does static site deployment meet the “real Linux service” goal?

Partly.

Static site with Nginx teaches:

- Nginx as a systemd service
    
- firewall
    
- logs
    
- permissions
    
- deployment
    
- virtual hosts
    
- HTTP
    

But it does **not** yet teach:

- app process management
    
- non-root application user
    
- environment variables
    
- backend logs
    
- database connection
    
- service failure/restart
    

So static sites are **phase 1**.

After that, phase 2 should be:

> Run a tiny backend app behind Nginx as a reverse proxy.

Example:

```text
Browser → Nginx :80 → localhost:3000 app service
```

But do not rush there. Static sites first are not too easy if you force him to explain the system.

## Best sequence

### Step 1: Static site by manual copy

Use `scp` once.

### Step 2: Static site by `rsync`

Use `rsync` repeatedly.

### Step 3: Put site in Git

Commit changes locally.

### Step 4: Deploy from Git-tracked local directory with `rsync`

This is the sweet spot.

### Step 5: Add multiple virtual hosts

Serve `site1.local`, `site2.local`, `site3.local`.

### Step 6: Break/fix labs

Permissions, firewall, bad config, logs.

### Step 7: Only then add GitHub

Push code to GitHub.

### Step 8: Later add CI/CD

GitHub Actions deploying over SSH.

## Minimum checklist before moving on

He should be able to explain:

1. Why files are under `/var/www`.
    
2. Which user owns the files.
    
3. Which user Nginx runs as.
    
4. Why Nginx can read but not write the files.
    
5. What `server_name` does.
    
6. What `root` does.
    
7. Why `nginx -t` comes before reload.
    
8. Difference between `systemctl reload nginx` and `restart nginx`.
    
9. Which firewall rule allows HTTP.
    
10. Difference between listening port and reachable port.
    
11. Where access logs are.
    
12. Where error logs are.
    
13. How `rsync --delete` can be dangerous.
    
14. Why FTP is a bad default.
    
15. What changed between two deployments according to Git.
    

If he can answer those, static sites did their job.


