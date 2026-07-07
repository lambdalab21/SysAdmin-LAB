# 05c — Install `site5-app` on `app01`

## Purpose

Move the app into a server-like layout on `app01`.

```text
/opt/site5-app/        app code
/var/lib/site5-app/    app data
```

---

# Big idea

Wrong:

```text
/opt/site5-app/app.py
/opt/site5-app/site5.db
```

Better:

```text
/opt/site5-app/app.py
/var/lib/site5-app/site5.db
```

Why?

```text
Code deployment may replace /opt/site5-app.
Data must survive code deployment.
```

---

# Part 1 — Create service user

On `app01`:

```bash
sudo useradd --system --home-dir /var/lib/site5-app --shell /sbin/nologin site5
```

If it already exists:

```bash
id site5
```

Stop and answer:

```text
Why not run as root?
Why create a separate site5 user?
Should site5 be a normal login user?
```

---

# Part 2 — Create directories

```bash
sudo mkdir -p /opt/site5-app
sudo mkdir -p /var/lib/site5-app
sudo chown -R deploy:deploy /opt/site5-app
sudo chown -R site5:site5 /var/lib/site5-app
ls -ld /opt/site5-app /var/lib/site5-app
```

Write:

```text
/opt/site5-app owner:
/var/lib/site5-app owner:
What this proves:
What this does not prove:
```

---

# Part 3 — Copy app code

From local project:

```bash
scp app.py deploy@app01:/opt/site5-app/
```

On `app01`:

```bash
ls -l /opt/site5-app/app.py
```

Write:

```text
Source:
Destination:
Owner:
What this proves:
```

---

# Part 4 — Run manually as site5

On `app01`:

```bash
sudo -u site5 SITE5_DB=/var/lib/site5-app/site5.db python3 /opt/site5-app/app.py
```

In another SSH session:

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'body=manual app01 test note' http://127.0.0.1:8000/notes
curl -s http://127.0.0.1:8000/ | grep 'manual app01 test note'
```

Stop with `Ctrl-C`.

Write:

```text
Manual run command:
GET evidence:
POST evidence:
What this proves:
What this does not prove:
```

---

# Part 5 — Prove database ownership

```bash
ls -l /var/lib/site5-app
ls -l /var/lib/site5-app/site5.db
sudo -u site5 test -w /var/lib/site5-app/site5.db && echo site5-can-write-db
```

If ownership is wrong:

```bash
sudo chown site5:site5 /var/lib/site5-app/site5.db
```

Write:

```text
Database path:
Database owner:
Write test:
What this proves:
```

---

# Final reflection

```text
App code directory:
Data directory:
App service user:
Who owns code:
Who owns data:
Manual run command:
Command that proved GET:
Command that proved POST:
Command that proved database file exists:
Why code and data are separated:
```
