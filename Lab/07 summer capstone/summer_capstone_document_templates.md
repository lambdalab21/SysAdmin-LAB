# End-of-Summer Capstone Document Templates

Use these templates during Days 1–10.

---

# README.md template

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

---

# ARCHITECTURE.md template

```markdown
# Architecture

## Request Flow

## Name Resolution

## Nginx

## systemd

## Flask App

## Jinja Templates

## SQLite Data

## Logs

## Backup Target
```

---

# DEPLOYMENT.md template

```markdown
# Deployment Runbook

## Goal

## Preconditions

## 1. Check Git status

## 2. Inspect diff

## 3. Commit intentionally

## 4. Back up database

## 5. rsync dry run

## 6. Deploy code

## 7. Restart service

## 8. Verify HTTP

## 9. Verify logs

## 10. Confirm data survived

## Rollback notes
```

---

# BACKUP_RESTORE.md template

```markdown
# Backup and Restore Runbook

## What is protected

## Live database path

## Backup directory

## Simple backup procedure

## Restore-test procedure

## Real restore procedure

## Verification

## Risks
```

---

# TROUBLESHOOTING.md template

```markdown
# Troubleshooting Guide

## General Method

symptom → layer → evidence → fix → verification

## Browser cannot open site6.local

## 502 Bad Gateway

## Page loads but POST fails

## Data disappeared

## App service fails to start

## Nginx reload fails

## Browser shows old content

## Permission denied

## Wrong site appears

## Commands by layer
```

---

# COMMANDS_I_CAN_EXPLAIN.md template

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

For each command:

```text
Command:
Question it answers:
Expected output:
What it proves:
What it does not prove:
Common mistake:
```

---

# ORAL_EXAM_ANSWERS.md template

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
