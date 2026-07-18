# Day 2 — Nginx, Host Header, and Browser Access

## Expected time

```text
Target: 90–120 minutes
Minimum: 60 minutes
Deep version: 2–3 hours with wrong-host and /etc/hosts troubleshooting
```

## Goal

Understand how Nginx chooses the correct site.

Key idea:

```text
IP address gets the request to the machine.
Port 80 gets the request to Nginx.
Host header tells Nginx which site name is being requested.
```


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


# Part 1 — Compare curl commands

Run:

```bash
curl -v http://app01/
curl -v -H 'Host: site6.local' http://app01/
curl -v http://site6.local/
```

Write:

```text
Which command connects to app01 directly?
Which command manually sets Host: site6.local?
Which command uses normal hostname resolution?
Which command is closest to what the browser does?
```

Expected:

```text
curl http://site6.local/ is closest to browser behavior.
```

# Part 2 — Inspect Nginx config

Run:

```bash
sudo nginx -t
sudo grep -R "server_name\|proxy_pass\|access_log\|error_log" /etc/nginx/conf.d/site6-app.conf
```

Write:

```text
server_name:
proxy_pass target:
access log:
error log:
What this proves:
What this does not prove:
```

# Part 3 — Browser access

On the browser machine, verify `/etc/hosts` or DNS:

```bash
getent hosts site6.local
```

Open:

```text
http://site6.local/
```

Then check Nginx access log on app01:

```bash
sudo tail -n 20 /var/log/nginx/site6.access.log
```

Write:

```text
Browser URL:
Access-log line:
HTTP status:
What this proves:
```

# Part 4 — Controlled failure: wrong Host

Run:

```bash
curl -v -H 'Host: wrong.local' http://app01/
```

Write:

```text
What page or status appeared?
Did Nginx choose site6?
How do I know?
What does this teach about server_name?
```

# Part 5 — Optional deeper challenge

Temporarily use a hostname that is not in `/etc/hosts`:

```bash
getent hosts fake-site6.local
curl -v http://fake-site6.local/
curl -v -H 'Host: site6.local' http://app01/
```

Explain:

```text
Why can one command fail while the other works?
Which layer failed: name resolution or Nginx?
```

# Deliverable

Update `ARCHITECTURE.md`:

```text
## Host Header and Nginx server_name

Explain:
- app01 is the machine
- port 80 reaches Nginx
- Host: site6.local chooses the site6 server block
- browser uses http://site6.local/
- curl can manually fake the Host header
```

# Completion checklist

```text
[ ] I can explain Host header.
[ ] I can explain server_name.
[ ] I can explain why browser uses http://site6.local/.
[ ] I checked Nginx config.
[ ] I checked access logs.
[ ] I tested a wrong Host header.
[ ] I updated ARCHITECTURE.md.
```
