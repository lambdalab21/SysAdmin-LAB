# End-of-Summer 10-Day Capstone — Own the Web Service

## Purpose

This is the final consolidation project after Lab 6.

The goal is not to start new tools.

The goal is to prove:

```text
I can explain, deploy, back up, restore, and troubleshoot one small web service.
```

Main capstone app:

```text
site6.local
→ Nginx
→ site6-app systemd service
→ Flask + Jinja app on 127.0.0.1:8000
→ SQLite database in /var/lib/site6-app/site6.db
```

Earlier sites are review material:

```text
site1 = scp deployment baseline
site2 = rsync deployment
site3 = Git-tracked rsync deployment
site4 = tiny backend app
site5 / site55 = app with forms and SQLite
site6 = Flask + Jinja CRUD capstone
```

---

# Expected time

Recommended daily time:

```text
Normal target: 90–120 minutes/day
Deep target: 2–3 hours/day
Day 6 break/fix challenge: 2–4 hours if done seriously
```

If only 60 minutes are available:

```text
Do fewer checks.
Write better explanations.
```

If 3 hours are available:

```text
Add one extra break/fix case.
Improve the documentation.
Explain the system aloud without notes.
```

---

# Final deliverables

By the end, the project should contain:

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

---

# What not to do in this 10-day block

Do not start:

```text
Docker
Kubernetes
Terraform
Ansible
GitHub Actions deployment
React/Vue/Svelte
FastAPI
cloud deployment
TLS certificates
authentication system
```

These are not bad tools. They are just not the goal of the final two weeks.

---

# Success standard

He is done when he can explain without notes:

```text
How a browser reaches the app.
What /etc/hosts or DNS does.
What Nginx does.
What systemd does.
Where code lives.
Where data lives.
Who owns the data.
How to deploy code safely.
How to back up the database.
How to restore the database.
How to troubleshoot 502.
How to troubleshoot failed POST.
How to prove the request reached each layer.
```

---

# Daily file list

```text
summer_capstone_day01_architecture_review.md
summer_capstone_day02_nginx_host_header.md
summer_capstone_day03_systemd_process_port_logs.md
summer_capstone_day04_permissions_database_backup.md
summer_capstone_day05_safe_deployment_git_rsync.md
summer_capstone_day06_break_fix_challenge.md
summer_capstone_day07_readme_architecture_docs.md
summer_capstone_day08_runbooks.md
summer_capstone_day09_troubleshooting_commands.md
summer_capstone_day10_oral_exam_cleanup.md
summer_capstone_document_templates.md
```
