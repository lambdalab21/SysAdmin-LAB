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
Which files can be replaced from Git? Git-replaceable: app.py, templates/, static/, requirements.txt 
Which file must not be deleted by code deployment? site6.db
Why not store site6.db inside /opt/site6-app? To keep DB out of/opt so that deploys can not wipe user data. 
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
Why not run Flask as root? Root is too priviliged, a bug becomes a system compromise. 
Should deploy user own the database? No. 
Should Nginx write the database? No. 
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
Why use an environment variable? Easy to change per environment without editing code. 
Why is local DB path different from server DB path? Local uses a convenient relative path; servers use a managed system path. 
What could go wrong if systemd points to the wrong DB path? App reads/writes the wrong file or fails to open the database. 
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
Why does Flask listen only on 127.0.0.1? Only the local reverse proxy should speak to it. 
Why does Nginx listen on port 80? Public HTTP traffic
Which process should be reachable from the LAN? Nginx
Which process should only be reachable locally? Flask/app process. 
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
Which log proves the browser reached Nginx? Nginx access log. 
Which log helps with proxy errors? Nginx error log. 
Which log shows Flask startup or crash errors? `journalctl -u site6-app`
Which log might show Python tracebacks? `journalctl -u site6-app`
```
