# Day 7 — README and Architecture Documentation

## Expected time

```text
Target: 90–150 minutes
Minimum: 60 minutes
Deep version: 2–3 hours with diagrams and rewritten explanations
```

## Goal

Write documentation that proves understanding.

A good README does not just say:

```text
This is a Flask app.
```

It explains:

```text
what it does
how it runs
where data lives
how to deploy
how to verify
how to troubleshoot
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


# Part 1 — Create README.md

Required structure:

```markdown
# site6 CRUD Capstone

## What this project is
## What I learned
## Architecture
## Local development
## Server deployment
## Important paths
## How to test
## How to back up data
## How to troubleshoot
## What I am not using yet
```

# Part 2 — Fill in important paths

Include:

```text
Local project:
Server code:
Server database:
systemd service:
Nginx config:
Nginx access log:
Nginx error log:
App journal:
Backup directory:
```

# Part 3 — Explain request flow

Write in your own words:

```text
When a browser opens http://site6.local/, name resolution maps site6.local to app01.
The browser connects to port 80.
Nginx receives the request.
Nginx uses server_name site6.local.
Nginx proxies to 127.0.0.1:8000.
The Flask app handles the route.
Jinja renders HTML.
SQLite stores task data.
```

# Part 4 — Explain what is intentionally delayed

Add:

```markdown
## What I am not using yet
```

Include:

```text
Docker
Kubernetes
Terraform
Ansible
GitHub Actions deployment
React/Vue frontend
FastAPI
authentication
cloud deployment
```

Explain why these were delayed.

# Part 5 — Self-check

Ask:

```text
Could another beginner understand what this project does?
Could I rebuild the app from this documentation?
Could I troubleshoot a 502 from this documentation?
Could I find the database from this documentation?
Could I safely deploy from this documentation?
```

# Completion checklist

```text
[ ] README.md exists.
[ ] It explains the project.
[ ] It explains architecture.
[ ] It lists important paths.
[ ] It explains deployment.
[ ] It explains backup.
[ ] It explains troubleshooting.
[ ] It says what is intentionally delayed.
```
