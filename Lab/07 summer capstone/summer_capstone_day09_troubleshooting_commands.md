# Day 9 — Troubleshooting Guide and Commands I Can Explain

## Expected time

```text
Target: 120–180 minutes
Minimum: 90 minutes
Deep version: 3 hours with symptom drills
```

## Goal

Create a troubleshooting guide organized by layer.

The goal is not to memorize commands.

The goal is to know which command answers which question.


---

# Daily depth rule

Do not stop because the commands ran successfully.

Stop only when you can explain:

```text
What question was I asking?
What command did I run?
What exact evidence did I get?
What does it prove?
What does it not prove?
What is the next narrow question?
```

If you finish in under 60 minutes, you probably did it too shallowly. Add one extra break/fix case or rewrite your explanation more clearly.


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

```text
Command:
Question it answers:
Expected output:
What it proves:
What it does not prove:
Common mistake:
```

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
What do I check if browser cannot resolve site6.local?
What do I check if Nginx returns 502?
What do I check if POST fails?
What do I check if data disappears after restart?
What do I check if systemd says failed?
What do I check if Nginx reload fails?
```

# Completion checklist

```text
[ ] COMMANDS_I_CAN_EXPLAIN.md is organized by layer.
[ ] Each command has what it proves and does not prove.
[ ] TROUBLESHOOTING.md is organized by symptom.
[ ] I can choose commands based on the question.
[ ] I can explain 502, failed POST, and name resolution problems.
```
