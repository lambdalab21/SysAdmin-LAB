# 06g — Infrastructure Review Questions for Flask CRUD

## Purpose

This is the final review for Phase 6.

Do not type commands mindlessly.

Answer questions until you can explain the whole system.

---

# Section 1 — Draw the system

Draw this from memory:

```text
browser
→ DNS or /etc/hosts
→ app01 port 80
→ Nginx server_name site6.local
→ proxy_pass 127.0.0.1:8000
→ Flask app process
→ SQLite database
```

Then answer:

```text
Which layer uses the hostname site6.local?
Which layer uses port 80?
Which layer uses port 8000?
Which layer reads templates?
Which layer writes the database?
Which layer records access logs?
```

---

# Section 2 — Code, data, config

Fill this in:

```text
App code directory:
Template directory:
Virtual environment:
Database path:
Systemd service file:
Nginx config file:
Nginx access log:
Nginx error log:
App journal command:
Backup directory:
```

Then explain:

```text
Which of these are code?
Which are data?
Which are configuration?
Which are logs?
Which are backups?
```

---

# Section 3 — Users and permissions

Answer:

```text
Which user deploys code?
Which user runs the app?
Which user owns the database?
Which user handles Nginx worker processes?
Why should the app not run as root?
Why should the database not be world-writable?
```

Commands to verify:

```bash
id site6
ls -ld /opt/site6-app
ls -ld /var/lib/site6-app
ls -l /var/lib/site6-app/site6.db
sudo systemctl status site6-app
```

---

# Section 4 — HTTP and CRUD

Match route to purpose:

```text
GET  /                     =
GET  /tasks/new            =
POST /tasks                =
GET  /tasks/<id>           =
GET  /tasks/<id>/edit      =
POST /tasks/<id>           =
POST /tasks/<id>/delete    =
```

Then answer:

```text
Which routes read data?
Which routes change data?
Why do create/update/delete use POST?
Why redirect after POST?
What could happen if refresh repeated a POST?
```

---

# Section 5 — Templates

Answer:

```text
Where do templates live?
Where does Jinja run?
What does the browser receive?
Why does template rendering belong on the server in this phase?
What problem does Jinja autoescaping help with?
```

Do not overstate security.

Say:

```text
Escaping output helps prevent raw user input from becoming HTML/JavaScript.
It is a basic safety habit, not a complete security system.
```

---

# Section 6 — Database

Answer:

```text
What database is used?
Where is the local development database?
Where is the server database?
How is the server database path configured?
Why is Git not enough to protect the database?
Why should database restore be tested?
```

Commands:

```bash
ls -lh /var/lib/site6-app/site6.db
sudo -u site6 test -r /var/lib/site6-app/site6.db && echo can-read
sudo -u site6 test -w /var/lib/site6-app/site6.db && echo can-write
```

---

# Section 7 — systemd

Answer:

```text
What does systemd do for this app?
What does systemd not prove?
What is ExecStart?
What is WorkingDirectory?
What does Environment=SITE6_DB do?
What is the difference between start and enable?
```

Commands:

```bash
sudo systemctl status site6-app
sudo systemctl restart site6-app
sudo journalctl -u site6-app --since "10 minutes ago"
systemctl is-enabled site6-app
```

---

# Section 8 — Nginx

Answer:

```text
What does Nginx do?
What is server_name?
What is proxy_pass?
Why does Nginx listen on port 80?
Why does Flask listen on 127.0.0.1:8000?
Which log proves a request reached Nginx?
Which log helps explain proxy errors?
```

Commands:

```bash
sudo nginx -t
sudo systemctl reload nginx
sudo tail -n 20 /var/log/nginx/site6.access.log
sudo tail -n 20 /var/log/nginx/site6.error.log
```

---

# Section 9 — Backup and restore

Answer:

```text
What file must be backed up?
Where are backups stored?
Why stop the app before a simple SQLite file copy?
What does restore into /tmp prove?
What does it not prove?
Why can a real restore lose newer data?
```

---

# Section 10 — Troubleshooting scenarios

## Symptom A

```text
Browser shows 502 Bad Gateway.
```

Commands:

```bash
sudo systemctl status site6-app
sudo ss -ltnp | grep 8000
sudo tail -n 20 /var/log/nginx/site6.error.log
```

## Symptom B

```text
Page loads, but creating a task fails.
```

Commands:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
ls -ld /var/lib/site6-app
ls -l /var/lib/site6-app/site6.db
```

## Symptom C

```text
Old tasks disappeared after deployment.
```

Commands:

```bash
grep SITE6_DB /etc/systemd/system/site6-app.service
ls -lh /var/lib/site6-app/site6.db
sudo -u site6 ls -lh /var/backups/site6-app
```

## Symptom D

```text
Browser cannot open http://site6.local/
```

Commands:

```bash
getent hosts site6.local
curl -v http://site6.local/
curl -v -H 'Host: site6.local' http://app01/
sudo tail -n 20 /var/log/nginx/site6.access.log
```

---

# Final oral exam

Explain without notes:

```text
A user opens http://site6.local/.
How does the request reach the Flask app?
How does the Flask app know which database to use?
How does the app create a task?
Why does it redirect after POST?
Where does the task live after the app restarts?
How do you back it up?
How do you deploy code without deleting it?
How do you troubleshoot a 502?
How do you troubleshoot a failed POST?
```

Completion checkpoint:

```text
[ ] I can draw the system.
[ ] I can explain every major file path.
[ ] I can explain every service user.
[ ] I can explain every HTTP route.
[ ] I can back up and restore data.
[ ] I can deploy code safely.
[ ] I can troubleshoot by layer.
```
