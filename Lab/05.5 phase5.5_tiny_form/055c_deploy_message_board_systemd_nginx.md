# 055c — Deploy Tiny Message Board with systemd and Nginx

## Purpose

Deploy the tiny message board on `app01`.

Target shape:

```text
browser
→ Nginx on port 80
→ site55 app on 127.0.0.1:8000
→ SQLite database in /var/lib/site55-app/site55.db
```

---

# Part 1 — Create service user and directories

On `app01`:

```bash
sudo useradd --system --home-dir /var/lib/site55-app --shell /sbin/nologin site55
```

If it already exists:

```bash
id site55
```

Create directories:

```bash
sudo mkdir -p /opt/site55-app
sudo mkdir -p /var/lib/site55-app
sudo chown -R deploy:deploy /opt/site55-app
sudo chown -R site55:site55 /var/lib/site55-app
```

Check:

```bash
ls -ld /opt/site55-app
ls -ld /var/lib/site55-app
id site55
```

Write:

```text
App code directory:

Data directory:

Service user:

Code owner:

Data owner:

What this proves:
```

---

# Part 2 — Copy app code

From local project:

```bash
scp app.py deploy@app01:/opt/site55-app/
```

On `app01`:

```bash
ls -l /opt/site55-app/app.py
```

Write:

```text
Source:

Destination:

Owner:

What this proves:
```

---

# Part 3 — Manual run as service user

On `app01`:

```bash
sudo -u site55 SITE55_DB=/var/lib/site55-app/site55.db python3 /opt/site55-app/app.py
```

In another terminal:

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'body=manual deploy test' http://127.0.0.1:8000/messages
curl -s http://127.0.0.1:8000/ | grep 'manual deploy test'
```

Stop manual app with Ctrl-C.

Write:

```text
Manual run command:

GET evidence:

POST evidence:

Database path:

What this proves:

What this does not prove:
```

---

# Part 4 — Create systemd service

Create:

```bash
sudo vi /etc/systemd/system/site55-app.service
```

Use:

```ini
[Unit]
Description=Tiny site55 message board
After=network.target

[Service]
Type=simple
User=site55
Environment=SITE55_DB=/var/lib/site55-app/site55.db
WorkingDirectory=/opt/site55-app
ExecStart=/usr/bin/python3 /opt/site55-app/app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## Stop and explain

```text
What user runs the app?

Where is the database path configured?

What command starts the app?

Why is WorkingDirectory /opt/site55-app?

Why not run as root?
```

---

# Part 5 — Start service

```bash
sudo systemctl daemon-reload
sudo systemctl start site55-app
sudo systemctl status site55-app
```

Test:

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'body=systemd deploy test' http://127.0.0.1:8000/messages
curl -s http://127.0.0.1:8000/ | grep 'systemd deploy test'
```

Check logs:

```bash
sudo journalctl -u site55-app --since "10 minutes ago"
sudo ss -ltnp | grep 8000
```

Write:

```text
Service state:

GET evidence:

POST evidence:

ss evidence:

journal evidence:

What this proves:
```

Enable only after testing:

```bash
sudo systemctl enable site55-app
```

---

# Part 6 — Create Nginx config

Create:

```bash
sudo vi /etc/nginx/conf.d/site55-app.conf
```

Use:

```nginx
server {
    listen 80;
    server_name site55.local;

    access_log /var/log/nginx/site55.access.log;
    error_log  /var/log/nginx/site55.error.log;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

Test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Write:

```text
Nginx config file:

server_name:

proxy_pass target:

nginx -t evidence:

What this proves:

What this does not prove:
```

---

# Part 7 — Test through Nginx

```bash
curl -v -H 'Host: site55.local' http://app01/
curl -v -H 'Host: site55.local' -X POST -d 'body=nginx form test' http://app01/messages
curl -s -H 'Host: site55.local' http://app01/ | grep 'nginx form test'
```

Check logs:

```bash
sudo tail -n 20 /var/log/nginx/site55.access.log
sudo tail -n 20 /var/log/nginx/site55.error.log
sudo journalctl -u site55-app --since "10 minutes ago"
```

Write:

```text
GET through Nginx evidence:

POST through Nginx evidence:

Access-log line:

Error-log evidence:

App journal evidence:

What this proves:
```

---

# Part 8 — Browser access

On the browser machine, add to `/etc/hosts`:

```text
APP01-IP site55.local
```

Then open:

```text
http://site55.local/
```

Submit a message in the form.

Verify from terminal:

```bash
curl -s -H 'Host: site55.local' http://app01/ | grep 'browser message text'
```

Write:

```text
Browser URL:

Message submitted:

curl evidence:

Nginx access-log evidence:
```

---

# Final reflection

```text
Task:

Service user:

Code directory:

Data directory:

Database path:

systemd service file:

Nginx config file:

Backend direct test:

Nginx test:

Browser test:

What systemd does:

What Nginx does:

What SQLite stores:
```

Completion checkpoint:

```text
[ ] App runs as site55.
[ ] Database lives in /var/lib/site55-app.
[ ] systemd manages the app.
[ ] Nginx proxies site55.local to the app.
[ ] GET works through Nginx.
[ ] POST works through Nginx.
[ ] Browser form submission works.
```
