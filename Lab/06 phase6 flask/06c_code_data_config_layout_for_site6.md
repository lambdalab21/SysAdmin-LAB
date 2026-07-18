# 06c — Code, Data, Config, and Runtime Layout for `site6`

## Purpose

Before deploying, plan where everything belongs.

A real service has more than code:

```text
code
data
configuration
process
logs
backups
```

---

# Target layout

```text
/opt/site6-app/                  app code
/opt/site6-app/.venv/            Python virtual environment
/var/lib/site6-app/site6.db      SQLite database
/etc/systemd/system/site6-app.service
/etc/nginx/conf.d/site6-app.conf
/var/backups/site6-app/
/var/log/nginx/site6.access.log
/var/log/nginx/site6.error.log
journalctl -u site6-app
```

---

# Part 1 — Code vs data

Code:

```text
app.py
templates/
static/
requirements.txt
```

Data:

```text
site6.db
```

Stop and answer:

```text
Which files can be replaced from Git?
Which file contains user-created data?
Which file must not be deleted by code deployment?
Why not store site6.db inside /opt/site6-app?
```

Expected:

```text
Code can be recreated from Git.
The database contains user data.
The database should live in /var/lib/site6-app.
```

---

# Part 2 — Service user model

Recommended:

```text
deploy user owns /opt/site6-app
site6 user owns /var/lib/site6-app
site6 user runs app process
nginx user handles public HTTP
```

Stop and answer:

```text
Why not run Flask as root?
Who should write the database?
Should deploy user own the database?
Should Nginx write the database?
```

---

# Part 3 — Config

The database path should come from an environment variable:

```text
SITE6_DB=/var/lib/site6-app/site6.db
```

In systemd:

```ini
Environment=SITE6_DB=/var/lib/site6-app/site6.db
```

This teaches:

```text
same code
different environment
different database path
```

Local:

```text
./data/site6.db
```

Server:

```text
/var/lib/site6-app/site6.db
```

Stop and explain:

```text
Why use an environment variable?
Why is local DB path different from server DB path?
What could go wrong if systemd points to the wrong DB path?
```

---

# Part 4 — Runtime

The app process should listen on:

```text
127.0.0.1:8000
```

Nginx listens on public port 80.

Explain:

```text
Why does Flask listen only on 127.0.0.1?
Why does Nginx listen on port 80?
Which process should be reachable from the LAN?
Which process should only be reachable locally?
```

---

# Part 5 — Logs

Nginx logs:

```text
/var/log/nginx/site6.access.log
/var/log/nginx/site6.error.log
```

App service logs:

```bash
journalctl -u site6-app
```

Stop and answer:

```text
Which log proves the browser reached Nginx?
Which log helps with proxy errors?
Which log shows Flask startup or crash errors?
Which log might show Python tracebacks?
```

---

# Final reflection

```text
Code directory:
Data directory:
Config file:
Systemd service:
Nginx config:
App user:
Database owner:
Nginx access log:
Nginx error log:
App journal command:
Backup directory:
One mistake this layout prevents:
```

Completion checkpoint:

```text
[ ] I can separate code from data.
[ ] I can explain service user ownership.
[ ] I can explain local vs server DB path.
[ ] I can explain why Nginx is public and Flask is local.
[ ] I can identify which log to check.
```
