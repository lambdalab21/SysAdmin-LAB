# Day 9 — Troubleshooting Guide and Commands I Can Explain

# Part 1 — Commands by layer

Create or improve:

```text
COMMANDS_I_CAN_EXPLAIN.md
```

Organize commands like this:

```markdown
# Commands I Can Explain

## Name resolution
## HTTP client
## Nginx
## systemd
## Process and port
## Files and permissions
## Database and backup
## Git and deployment
## Logs
```

# Part 2 — Fill in command cards

For each command, use:

Include at least:

```bash
getent hosts site6.local
curl -v -H 'Host: site6.local' http://app01/
sudo nginx -t
sudo systemctl status site6-app
sudo journalctl -u site6-app --since "10 minutes ago"
sudo ss -ltnp | grep 8000
ls -ld /var/lib/site6-app
rsync -avn --delete ...
git status
git diff
```

# Part 3 — Troubleshooting guide

Create or improve:

```text
TROUBLESHOOTING.md
```

Use symptom-based sections:

```markdown
# Troubleshooting Guide

## Browser cannot open site6.local
## 502 Bad Gateway
## Page loads but POST fails
## Data disappeared
## App service fails to start
## Nginx reload fails
## Browser shows old content
## Permission denied
## Wrong site appears
```

# Part 4 — For each symptom

Use this structure:

```text
Symptom:
Likely layers:
First command:
Second command:
Best log:
Common causes:
Fix examples:
Verification:
```

# Part 5 — Mini oral drill

Answer without commands first, then verify.

```text
What do I check if browser cannot resolve site6.local? The browser can't resolve `site6.local`. 
What do I check if Nginx returns 502? Check whether the backend process is running and listening, then check Nginx's error log for the specific connect failure. 
What do I check if POST fails? Check the app/service logs for errors, and check filesystem permissions/ownership on the database and data directory. 
What do I check if data disappears after restart? CHeck whether the app is writing to the expected persistent path rather than a temp location, and check that a deployment didn't overwrite the data directory. 
What do I check if systemd says failed? Run `systemctl status` for the failure reason and `journalctl -u site6-app` for the actual error. 
What do I check if Nginx reload fails? Run `sudo nginx -t` to test the config syntax before reloading. 
```