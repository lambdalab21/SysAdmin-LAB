#LAB #deployment #backend
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
Why not run as root? Running as the root is dangerous. A bug could delete or damage the entire system. 
Why create a separate site5 user? Principle of least privilege. The app only has permissions if they're needed. 
Should site5 be a normal login user? No. It should be a system user with no login shell. 
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
Source: Local app.py
Destination: /opt/site5-app/app.py on app01
Owner: deploy:deploy
What this proves: This application's code is successfully deployed to the standard /opt location.
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
GET evidence: Server returns 200 OK with HTML page. 
POST evidence: Note appears when you get / again. 
What this proves: The app runs correctly udner the dedicated site5 user and it uses the proper data directory. 
What this does not prove: That it survives, reboots, or runs automatically as a service. 
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
Database path: /var/lib/site5-app/site5.db
Database owner: site5:site5
What this proves: The service user can read and write its own database file. 
```

---

# Final reflection

```text
App code directory: /opt/site5-app/
Data directory: /var/lib/site5-app/
App service user:site5
Who owns code: deploy:deploy
Who owns data: site5:site5
Command that proved GET: curl -v http://127.0.0.1:8000
Command that proved database file exists: ls -l /var/lib/site5-app/site5.db
Why code and data are separated: Code in /opt can be overwritten during deployments but data in /var/lib must persist across updates and restarts. 
```
