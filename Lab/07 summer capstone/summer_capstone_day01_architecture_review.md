# Day 1 — Architecture Review: Browser to Database

# Part 1 — Draw the system

Create or update:

```text
ARCHITECTURE.md
```

Draw this from memory first:

```text
[Browser]
   |
   | http://site6.local/
   v
[Name resolution: /etc/hosts or DNS]
   |
   v
[app01:80]
   |
   v
[Nginx server block: site6.local]
   |
   | proxy_pass http://127.0.0.1:8000
   v
[Flask app process: site6-app]
   |
   v
[SQLite: /var/lib/site6-app/site6.db]
```

# Part 2 — Evidence commands

Run:

```bash
getent hosts site6.local
curl -v -H 'Host: site6.local' http://app01/
sudo systemctl status site6-app
sudo ss -ltnp | grep 8000
ls -lh /var/lib/site6-app/site6.db
```

# Part 3 — Optional deeper challenge

Run these and explain the difference:

```bash
curl -v http://app01/
curl -v -H 'Host: site6.local' http://app01/
curl -v http://site6.local/
```

# Deliverable

Update `ARCHITECTURE.md` with sections:

```text
# Architecture

## Request Flow
## Name Resolution
## Nginx
## systemd
## Flask App
## SQLite Data
## Logs
## Backup Target
```