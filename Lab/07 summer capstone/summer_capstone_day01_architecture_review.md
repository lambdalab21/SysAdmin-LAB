# Day 1 — Architecture Review: Browser to Database

## Expected time

```text
Target: 90–120 minutes
Minimum: 60 minutes if focused
Deep version: 2–3 hours with a hand-drawn diagram and rewritten explanation
```

## Goal

Draw and explain the whole request path.

Target:

```text
browser
→ /etc/hosts or DNS
→ app01 port 80
→ Nginx server_name site6.local
→ proxy_pass http://127.0.0.1:8000
→ site6-app systemd service
→ Flask app
→ Jinja templates
→ SQLite database
```

## Why this matters

An infrastructure person must know where a request goes and which layer is responsible for each part.

If you cannot draw the system, troubleshooting becomes guessing.


---

# Daily depth rule

Do not stop because the commands ran successfully.

Stop only when you can explain:

```text
What question was I asking?
What command did I run?
What exact evidence did I get?
What does it prove?
What does it not prove?
What is the next narrow question?
```

If you finish in under 60 minutes, you probably did it too shallowly. Add one extra break/fix case or rewrite your explanation more clearly.


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

Then check your drawing against the machine.

# Part 2 — Evidence commands

Run:

```bash
getent hosts site6.local
curl -v -H 'Host: site6.local' http://app01/
sudo systemctl status site6-app
sudo ss -ltnp | grep 8000
ls -lh /var/lib/site6-app/site6.db
```

For each command, write:

```text
Command:
Exact output:
What this proves:
What this does not prove:
```

# Part 3 — Feynman explanation

Explain to a beginner:

```text
When I open http://site6.local/, my computer first needs an IP address.
Then it connects to app01 on port 80.
Nginx receives the request.
Nginx uses the Host header to choose the site6 server block.
Nginx forwards the request to the Flask app on 127.0.0.1:8000.
Flask runs route code.
Jinja renders HTML.
SQLite stores persistent data.
```

Rewrite this in your own words. Do not merely copy it.

# Part 4 — Optional deeper challenge

Run these and explain the difference:

```bash
curl -v http://app01/
curl -v -H 'Host: site6.local' http://app01/
curl -v http://site6.local/
```

Answer:

```text
Which command tests name resolution?
Which command manually forces Host: site6.local?
Which command is closest to browser behavior?
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

# Completion checklist

```text
[ ] I drew the system.
[ ] I proved name resolution.
[ ] I proved Nginx access.
[ ] I proved the app service is running.
[ ] I proved the app listens on port 8000.
[ ] I located the database.
[ ] I updated ARCHITECTURE.md.
[ ] I can explain the request path aloud without notes.
```
