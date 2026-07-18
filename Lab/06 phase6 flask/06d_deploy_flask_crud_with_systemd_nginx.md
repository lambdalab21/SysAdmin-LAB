# 06d — Deploy Flask CRUD App with systemd and Nginx

## Purpose

Deploy the Flask + Jinja CRUD app on `app01`.

Target:

```text
browser
→ Nginx port 80
→ Flask app on 127.0.0.1:8000
→ SQLite database in /var/lib/site6-app/site6.db
```

---

# Part 1 — Create service user and directories

On `app01`:

```bash
sudo useradd --system --home-dir /var/lib/site6-app --shell /sbin/nologin site6
```

If it exists:

```bash
id site6
```

Create directories:

```bash
sudo mkdir -p /opt/site6-app
sudo mkdir -p /var/lib/site6-app
sudo mkdir -p /var/backups/site6-app

sudo chown -R deploy:deploy /opt/site6-app
sudo chown -R site6:site6 /var/lib/site6-app
sudo chown -R site6:site6 /var/backups/site6-app
```

Check:

```bash
ls -ld /opt/site6-app /var/lib/site6-app /var/backups/site6-app
id site6
```

Write:

```text
Service user:
Code directory owner:
Data directory owner:
Backup directory owner:
What this proves:
```

---

# Part 2 — Create requirements file locally

In local project:

```bash
cd ~/projects/site6-crud
. .venv/bin/activate
python -m pip freeze > requirements.txt
cat requirements.txt
```

Commit if using Git:

```bash
git add requirements.txt
git commit -m "Add requirements file"
```

---

# Part 3 — Copy code to server

From local project:

```bash
rsync -av --delete   --exclude '.git'   --exclude '.venv'   --exclude 'data'   ./ deploy@app01:/opt/site6-app/
```

Stop and explain:

```text
Why exclude .venv?
Why exclude data?
Why exclude .git?
Which directory is being updated?
Which directory must not be touched?
```

Check on app01:

```bash
ssh deploy@app01 'find /opt/site6-app -maxdepth 3 -type f | sort'
```

---

# Part 4 — Create virtual environment on server

On `app01`:

```bash
cd /opt/site6-app
python3 -m venv .venv
. .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Check:

```bash
/opt/site6-app/.venv/bin/python -c 'import flask; print(flask.__version__)'
```

Write:

```text
Server venv path:
Flask import evidence:
What this proves:
What this does not prove:
```

---

# Part 5 — Manual run as site6

Run on app01:

```bash
sudo -u site6 SITE6_DB=/var/lib/site6-app/site6.db /opt/site6-app/.venv/bin/python /opt/site6-app/app.py
```

In another terminal:

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'title=manual server task' -d 'body=manual test' http://127.0.0.1:8000/tasks
curl -s http://127.0.0.1:8000/ | grep 'manual server task'
```

Stop manual app with Ctrl-C.

---

# Part 6 — Create systemd service

Create:

```bash
sudo vi /etc/systemd/system/site6-app.service
```

Use:

```ini
[Unit]
Description=Flask site6 CRUD app
After=network.target

[Service]
Type=simple
User=site6
Environment=SITE6_DB=/var/lib/site6-app/site6.db
WorkingDirectory=/opt/site6-app
ExecStart=/opt/site6-app/.venv/bin/python /opt/site6-app/app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Start:

```bash
sudo systemctl daemon-reload
sudo systemctl start site6-app
sudo systemctl status site6-app
```

Test:

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'title=systemd task' -d 'body=systemd test' http://127.0.0.1:8000/tasks
curl -s http://127.0.0.1:8000/ | grep 'systemd task'
```

Logs:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
sudo ss -ltnp | grep 8000
```

Enable only after testing:

```bash
sudo systemctl enable site6-app
```

---

# Part 7 — Create Nginx config

Create:

```bash
sudo vi /etc/nginx/conf.d/site6-app.conf
```

Use:

```nginx
server {
    listen 80;
    server_name site6.local;

    access_log /var/log/nginx/site6.access.log;
    error_log  /var/log/nginx/site6.error.log;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

Test:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

# Part 8 — Test through Nginx

```bash
curl -v -H 'Host: site6.local' http://app01/
curl -v -H 'Host: site6.local' -X POST -d 'title=nginx task' -d 'body=nginx test' http://app01/tasks
curl -s -H 'Host: site6.local' http://app01/ | grep 'nginx task'
```

Check logs:

```bash
sudo tail -n 20 /var/log/nginx/site6.access.log
sudo tail -n 20 /var/log/nginx/site6.error.log
sudo journalctl -u site6-app --since "10 minutes ago"
```

---

# Final reflection

```text
Code directory:
Data directory:
Service user:
Python venv path:
systemd service file:
Nginx config file:
Direct backend test:
Nginx test:
Browser test:
Strongest evidence that deployment works:
Strongest evidence that data is persistent:
```

Completion checkpoint:

```text
[ ] Flask app runs locally on app01.
[ ] systemd manages site6-app.
[ ] Nginx proxies site6.local to the app.
[ ] CRUD works through Nginx.
[ ] Logs show evidence.
```
