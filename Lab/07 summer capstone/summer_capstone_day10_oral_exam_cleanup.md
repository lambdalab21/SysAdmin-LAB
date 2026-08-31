# Day 10 — Oral Exam, Cleanup, and Final Reflection


---

# Part 1 — Oral exam

Answer without notes first.

Then check notes.

## Architecture

```text
A user opens http://site6.local/.
Explain every step until SQLite is reached.
```
1.) The browser resolves `site6.local` to the server IP.
2.) It sends HTTP GET to port 80. 
3.) Nginx accepts the connection on Port 80, matches `server_name site6.local`, and uses `proxy_pass` to forward the request to the app backend. 
4.) The systemd unit `site6-app` keeps the Flask process running. 
5.) Flask handles the route and opens the SQLite database. 
6.) SQLite reads/writes the file and returns data. This response flows back through flask, nginx, and to the browser. 

## Nginx

```text
What does Nginx do? Reerses proxy and web server. 
What is server_name? The hostname this `server` block responds to. 
What is proxy_pass? Directive that forwards the request to the upstream app. 
Why does Nginx listen on port 80? Standard unencrypted HTTP port so browsers can reach the site without specifying a port. 
```

## systemd

```text
What does systemd do? Starts, stops, and supervises the `site6-app` service. Manages its lifecycle and environment. 
What is ExecStart? The exact command that launches the application process. 
What is Environment=SITE6_DB? Sets the environment variable that tells the app where the SQLite files live. 
```

## Troubleshooting

```text
How do you troubleshoot failed POST? App error, permission, or CSRF/validation issue -> check app logs and Nginx error log. 
How do you troubleshoot wrong site showing? `server_name` mismatch or missing HOST reader -> verify NGinx configurations and `curl -H 'host: site6.local'
```

# Part 2 — Write ORAL_EXAM_ANSWERS.md

Create:

```text
ORAL_EXAM_ANSWERS.md
```

Use:

```markdown
# Oral Exam Answers

## Request flow
## Nginx
## systemd
## Flask and Jinja
## SQLite
## Deployment
## Backup and restore
## Troubleshooting
## What I intentionally delayed
```

# Part 3 — Cleanup

Check app status:

```bash
sudo systemctl status site6-app
curl -H 'Host: site6.local' http://app01/
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Check project Git:

```bash
git status
git log --oneline -n 5
```


# Part 4 — Final package

Confirm these files exist:

```text
DEPLOYMENT.md
TROUBLESHOOTING.md
COMMANDS_I_CAN_EXPLAIN.md
ORAL_EXAM_ANSWERS.md
```

Optional:

```text
LESSONS_LEARNED.md
```

# Part 5 — Final reflection

Write:

```text
The most important thing I learned: The full request path from browser to SQLite. 
The most confusing thing at first: How Nginx, systemd, and the apps fit together. 
The command I understand best now: Systemctl status / restart and the proxy flow. 
The failure I can diagnose best now: 502 Bad Gateway. 
The failure I still need to practice: Intermittent data or permission issues. 
The tool I am glad I did not rush into: Docker/containers. 
What I am ready to learn next: More robust backup automation and monitoring. 
```