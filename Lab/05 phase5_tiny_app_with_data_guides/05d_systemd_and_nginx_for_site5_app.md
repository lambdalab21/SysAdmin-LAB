#LAB #deployment #backend
# 05d — systemd and Nginx for `site5-app`

## Purpose

Turn the manually tested notes app into a managed service behind Nginx.

Target:

```text
browser → Nginx port 80 → site5-app 127.0.0.1:8000 → /var/lib/site5-app/site5.db
```

---

# Part 1 — Confirm manual app worked

```bash
sudo -u site5 SITE5_DB=/var/lib/site5-app/site5.db python3 /opt/site5-app/app.py
curl http://127.0.0.1:8000/
```

Stop with `Ctrl-C`.

Write:

```text
Manual command:
curl result:
What this proves:
What this does not prove:
```

---

# Part 2 — Create systemd service

```bash
sudo vi /etc/systemd/system/site5-app.service
```

Use:

```ini
[Unit]
Description=Tiny site5 notes app
After=network.target

[Service]
Type=simple
User=site5
Environment=SITE5_DB=/var/lib/site5-app/site5.db
WorkingDirectory=/opt/site5-app
ExecStart=/usr/bin/python3 /opt/site5-app/app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Stop and answer:

```text
What user runs the app?
Where is the database path set?
What command starts the app?
Why is it not root?
```

---

# Part 3 — Start service

```bash
sudo systemctl daemon-reload
sudo systemctl start site5-app
sudo systemctl status site5-app
```

Write:

```text
Service state:
Main PID:
What this proves:
What this does not prove:
```

---

# Part 4 — Test backend directly

```bash
curl -v http://127.0.0.1:8000/
curl -v -X POST -d 'body=systemd test note' http://127.0.0.1:8000/notes
curl -s http://127.0.0.1:8000/ | grep 'systemd test note'
sudo ss -ltnp | grep 8000
sudo journalctl -u site5-app --since '10 minutes ago'
```

Write:

```text
GET evidence:
POST evidence:
ss evidence:
journal evidence:
What this proves:
```

---

# Part 5 — Enable service

```bash
sudo systemctl enable site5-app
systemctl is-enabled site5-app
```

Answer:

```text
What does start do?
What does enable do?
Are they the same?
```

---

# Part 6 — Create Nginx config

```bash
sudo vi /etc/nginx/conf.d/site5-app.conf
```

Use:

```nginx
server {
    listen 80;
    server_name site5.local;

    access_log /var/log/nginx/site5.access.log;
    error_log  /var/log/nginx/site5.error.log;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

Stop and answer:

```text
What port does Nginx listen on?
What server_name is used?
Where does proxy_pass send requests?
Which log shows browser requests?
Which log shows Nginx proxy errors?
```

---

# Part 7 — Test and reload Nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Write:

```text
nginx -t output:
What nginx -t proves:
What nginx -t does not prove:
```

---

# Part 8 — Test through Nginx

```bash
curl -v -H 'Host: site5.local' http://app01/
curl -v -H 'Host: site5.local' -X POST -d 'body=nginx proxy test note' http://app01/notes
curl -s -H 'Host: site5.local' http://app01/ | grep 'nginx proxy test note'
sudo tail -n 20 /var/log/nginx/site5.access.log
sudo tail -n 20 /var/log/nginx/site5.error.log
sudo journalctl -u site5-app --since '10 minutes ago'
```

Write:

```text
Nginx GET evidence:
Nginx POST evidence:
Access-log evidence:
Error-log evidence:
App journal evidence:
What this proves:
```

---

# Browser reminder

For a browser, add to the browser machine's `/etc/hosts`:

```text
app01-IP site5.local
```

Then open:

```text
http://site5.local/
```

---

# Final reflection

```text
Service file:
Nginx config file:
App service user:
Database path:
Backend direct curl:
Nginx curl:
Access-log evidence:
App journal evidence:
What systemd does:
What Nginx does:
What SQLite stores:
```
