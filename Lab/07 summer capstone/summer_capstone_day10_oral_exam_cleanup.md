# Day 10 — Oral Exam, Cleanup, and Final Reflection

## Expected time

```text
Target: 90–150 minutes
Minimum: 60 minutes
Deep version: 2–3 hours with a full no-notes oral exam
```

## Goal

Prove ownership.

Today is not about adding features.

Today is about answering clearly:

```text
What did I build?
How does it work?
How do I deploy it?
How do I protect data?
How do I troubleshoot it?
```


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


# Part 1 — Oral exam

Answer out loud without notes first.

Then check notes.

## Architecture

```text
A user opens http://site6.local/.
Explain every step until SQLite is reached.
```

## Nginx

```text
What does Nginx do?
What is server_name?
What is proxy_pass?
Why does Nginx listen on port 80?
```

## systemd

```text
What does systemd do?
What is ExecStart?
What is Environment=SITE6_DB?
What is the difference between start and enable?
```

## Database

```text
Where is the database?
Who owns it?
Why is it not inside /opt/site6-app?
How do you back it up?
How do you test restore?
```

## Deployment

```text
What is the safe deployment sequence?
Why run git diff?
Why run rsync dry run?
Why check old data after deployment?
```

## Troubleshooting

```text
How do you troubleshoot 502?
How do you troubleshoot failed POST?
How do you troubleshoot wrong site showing?
How do you troubleshoot data missing?
```

# Part 2 — Write ORAL_EXAM_ANSWERS.md

Create:

```text
ORAL_EXAM_ANSWERS.md
```

Use:

```markdown
# Oral Exam Answers

## Request flow
## Nginx
## systemd
## Flask and Jinja
## SQLite
## Deployment
## Backup and restore
## Troubleshooting
## What I intentionally delayed
```

# Part 3 — Cleanup

Check app status:

```bash
sudo systemctl status site6-app
curl -H 'Host: site6.local' http://app01/
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Check project Git:

```bash
git status
git log --oneline -n 5
```

Write:

```text
Service health:
HTTP health:
Error log:
Git status:
Anything to clean up:
```

# Part 4 — Final package

Confirm these files exist:

```text
README.md
ARCHITECTURE.md
DEPLOYMENT.md
BACKUP_RESTORE.md
TROUBLESHOOTING.md
COMMANDS_I_CAN_EXPLAIN.md
ORAL_EXAM_ANSWERS.md
```

Optional:

```text
LESSONS_LEARNED.md
```

# Part 5 — Final reflection

Write:

```text
The most important thing I learned:
The most confusing thing at first:
The command I understand best now:
The failure I can diagnose best now:
The failure I still need to practice:
The tool I am glad I did not rush into:
What I am ready to learn next:
```

# Final completion standard

You are done when you can say:

```text
I can explain my app from browser to database.
I can deploy code without deleting data.
I can back up and test restore.
I can troubleshoot by layer.
I can explain the commands I use.
I know which tools I delayed and why.
```

That is a successful summer.
```
