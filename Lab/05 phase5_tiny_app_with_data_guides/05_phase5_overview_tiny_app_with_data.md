#LAB #deployment #backend
# Phase 5 Overview — Tiny Backend App with Data

## Purpose

Phase 4 taught:

```text
browser → Nginx → backend process → systemd → logs
```

Phase 5 adds:

```text
persistent data
```

The new lesson is simple but important:

```text
code can be redeployed
data must be protected
```

In this phase, the app stores notes in SQLite.

---

# Feynman version

```text
A static site is printed paper.
A backend app is a clerk answering questions.
An app with data is a clerk with a notebook.
Replacing the clerk is okay.
Losing the notebook is serious.
```

In this phase:

```text
Python app = clerk
SQLite database = notebook
Nginx = front desk
systemd = supervisor
backup = photocopy of the notebook
```

---

# What Phase 5 teaches

```text
state
SQLite as a file database
code/data separation
Linux directory layout
service users and permissions
safe deployment
backup and restore
troubleshooting data-related failures
```

Recommended layout:

```text
/opt/site5-app/              app code
/var/lib/site5-app/          persistent data
/etc/systemd/system/         systemd service
/etc/nginx/conf.d/           Nginx config
/var/log/nginx/              Nginx logs
journalctl -u site5-app      app service logs
```

---

# Phase 5 files

Use in order:

```text
05a_state_sqlite_and_directory_layout.md
05b_build_tiny_notes_app_locally.md
05c_install_site5_app_on_app01.md
05d_systemd_and_nginx_for_site5_app.md
05e_backup_and_restore_sqlite_data.md
05f_safe_code_deployment_without_destroying_data.md
05g_break_fix_site5_app_with_data.md
```

---

# What not to add yet

Do not add:

```text
login system
passwords
sessions
file uploads
React/Vue
REST API design
Docker
MySQL/PostgreSQL
Kubernetes
Terraform
GitHub Actions deployment
```

Phase 5 should stay small enough that every layer can be explained.

---

# Evidence habit

Before important commands:

```text
Question:
Command:
Expected evidence:
Risk if I am wrong:
```

After commands:

```text
Exact output:
What this proves:
What this does not prove:
Next narrow question:
```

Completion standard:

```text
I can explain where code lives, where data lives, who owns the data, how to back it up, how to test restore, and how to prove Nginx/app/database behavior with evidence.
```
