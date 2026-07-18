# Phase 6 Overview — Flask + Jinja CRUD App for Infrastructure Thinking

## Purpose

This phase introduces a real server-rendered CRUD application.

But the purpose is **not** simply:

```text
learn Flask
```

The purpose is:

```text
use a small Flask + Jinja + SQLite app to think like an infrastructure person
```

You will learn how a normal web app is structured, deployed, backed up, restarted, logged, and troubleshot.

---

# Previous phases

You already learned:

```text
static site
→ Nginx
→ scp / rsync / Git-tracked deployment
```

Then:

```text
tiny backend app
→ process
→ port
→ systemd
→ Nginx reverse proxy
→ logs
```

Then:

```text
tiny form app
→ GET
→ POST
→ redirect-after-POST
→ SQLite persistence
→ backup/restore
```

Now you will learn:

```text
CRUD app
→ routes
→ templates
→ database records
→ validation
→ deployment
→ service management
→ troubleshooting
```

---

# Why Flask + Jinja now?

Raw Python was useful for seeing the machine clearly:

```text
process
port
curl
systemd
Nginx
```

But for a CRUD app, raw Python becomes distracting.

You would spend too much time on:

```text
manual routing
manual HTML building
manual form parsing
manual redirect handling
```

Flask + Jinja lets you focus on the normal web-app shape:

```text
route
→ database query
→ template
→ HTML response
```

---

# What CRUD means

CRUD means:

```text
Create
Read
Update
Delete
```

In this app:

```text
Create: add a task
Read: list tasks and view one task
Update: edit a task or mark it done
Delete: delete or archive a task
```

But the deeper infrastructure lesson is:

```text
Every operation changes or reads persistent state.
Persistent state must be protected.
```

---

# Target architecture

```text
Browser
→ Nginx on port 80
→ Flask app on 127.0.0.1:8000
→ SQLite database in /var/lib/site6-app/site6.db
```

Recommended layout:

```text
/opt/site6-app/               application code
/var/lib/site6-app/           SQLite database
/etc/systemd/system/          systemd service
/etc/nginx/conf.d/            Nginx config
/var/log/nginx/               Nginx logs
journalctl -u site6-app       app service logs
```

---

# Feynman version

Explain it to a younger student:

```text
Nginx is the front desk.
Flask is the worker who knows the rules.
Jinja is the form printer.
SQLite is the notebook.
systemd is the supervisor.
Logs are the security camera.
Backups are photocopies of the notebook.
```

---

# Phase 6 files

Use these in order:

```text
06a_flask_jinja_crud_concepts.md
06b_build_flask_jinja_crud_locally.md
06c_code_data_config_layout_for_site6.md
06d_deploy_flask_crud_with_systemd_nginx.md
06e_backup_restore_and_safe_deploy_flask_crud.md
06f_break_fix_flask_crud_infra.md
06g_infra_review_questions_for_flask_crud.md
```

Recommended pace:

```text
Day 1: 06a
Day 2-3: 06b
Day 4: 06c
Day 5: 06d
Day 6: 06e
Day 7: 06f
Day 8: 06g
```

---

# What not to add yet

Do not add these yet:

```text
Flask-Login
passwords
sessions
SQLAlchemy
Alembic
WTForms
Blueprints
REST API
JavaScript fetch
React/Vue/Svelte
Docker
Gunicorn complexity at first
GitHub Actions deployment
Kubernetes
Terraform
```

This phase is already enough.

---

# Completion standard

By the end, you should be able to explain:

```text
How a route maps a URL to server-side code.
How a template turns server-side data into HTML.
How a form creates or updates database records.
How redirect-after-POST prevents duplicate submissions.
Where code lives.
Where data lives.
Which user runs the app.
Which user owns the database.
How systemd starts/restarts the app.
How Nginx reaches the app.
How to back up and restore data.
How to deploy code without deleting data.
How to use logs to find which layer broke.
```

---

# Evidence habit

Before each important command:

```text
Question:
Command:
Expected evidence:
Risk if I am wrong:
```

After each command:

```text
Exact output:
What this proves:
What this does not prove:
Next narrow question:
```

Do not write:

```text
it works
done
probably
fine
```

Write evidence.
