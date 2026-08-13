# 06g — Infrastructure Review Questions for Flask CRUD

## Purpose

Answer questions until you can explain the whole system.

---

# Section 1 — Draw the system

Answer:

```text
Which layer uses the hostname site6.local? Hostname site6.local: nginx(server_name)
Which layer uses port 80? Port 80: nginx
Which layer uses port 8000? Flask app. 
Which layer reads templates? Flask app. 
Which layer writes the database? Flask app. 
Which layer records access logs? Nginx. 
```

---

# Section 2 — Code, data, config

Then explain:

```text
Which of these are code? app code directory, templates, and the virtual environment. 
Which are data? database path. 
Which are configuration? systemd service file, Nginx config file. 
Which are logs? Nginx access/error logs. 
```

---

# Section 3 — Users and permissions

Answer:

```text
Which user deploys code? The deploy user. 
Which user runs the app? site6 
Which user owns the database? site6 
Which user handles Nginx worker processes? www-data. 
Why should the app not run as root? It limits the damage caused if the app is compromised. 
Why should the database not be world-writable? Prevents other users from modifying/deleting data in an unwanted, harmful manner or context. 
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

Answer:

```text
Which routes read data? GET routes. 
Which routes change data? POST routes. 
Why do create/update/delete use POST? They change state, GET should be safe. 
Why redirect after POST? Prevents accidental rresubmissions on refreshes. 
What could happen if refresh repeated a POST? Because it could create duplicate records or repeat the action. 
```

---

# Section 5 — Troubleshooting scenarios

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
How does the request reach the Flask app?  browser -> DNS/hosts -> NGinx:80 -> proxy_pass -> flask:8000
How does the Flask app know which database to use? From site6_db environment variable set by systemd
How do you deploy code without deleting it? update code under /opt and keep the separate data directory, restart service. 
How do you troubleshoot a 502? Check app status, port 8000, nginx error log. 
How do you troubleshoot a failed POST? Check the app journal and database permission. 
```