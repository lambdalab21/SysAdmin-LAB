# Day 3 — systemd, Process, Port, and App Logs

## Expected time

```text
Target: 90–150 minutes
Minimum: 60 minutes
Deep version: 2–3 hours with stop/start/restart and log comparison
```

## Goal

Understand the app as a managed Linux service.

Key idea:

```text
A web app is a running process.
systemd manages the process.
ss proves the process listens on a port.
journalctl shows service logs.
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


# Part 1 — Service status

Run:

```bash
sudo systemctl status site6-app
systemctl is-enabled site6-app
```

Write:

```text
Service state:
Main PID:
Enabled status:
What start means:
What enable means:
What this proves:
What this does not prove:
```

# Part 2 — Port evidence

Run:

```bash
sudo ss -ltnp | grep 8000
```

Write:

```text
Listening address:
Listening port:
Process:
What this proves:
```

Expected:

```text
The app should listen on 127.0.0.1:8000.
```

If it shows `0.0.0.0:8000`, explain why that is different.

# Part 3 — Direct backend test

Run:

```bash
curl -v http://127.0.0.1:8000/
```

Write:

```text
HTTP status:
Response evidence:
What this proves:
What this does not prove:
```

Expected:

```text
This proves the backend app responds locally.
It does not prove Nginx can proxy to it.
```

# Part 4 — Journal logs

Run:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
```

Make a request:

```bash
curl -H 'Host: site6.local' http://app01/
```

Check journal again:

```bash
sudo journalctl -u site6-app --since "2 minutes ago"
```

Write:

```text
Journal evidence:
What app logs show:
What app logs do not show:
```

# Part 5 — Controlled failure: stop service

Break:

```bash
sudo systemctl stop site6-app
```

Test through Nginx:

```bash
curl -v -H 'Host: site6.local' http://app01/
```

Check:

```bash
sudo systemctl status site6-app
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Fix:

```bash
sudo systemctl start site6-app
```

Verify:

```bash
curl -H 'Host: site6.local' http://app01/
```

Write:

```text
Symptom:
systemd evidence:
Nginx error evidence:
Fix:
Verification:
```

# Deliverable

Create or improve:

```text
COMMANDS_I_CAN_EXPLAIN.md
```

Add:

```text
systemctl status
systemctl start
systemctl stop
systemctl restart
systemctl enable
journalctl -u
ss -ltnp
curl http://127.0.0.1:8000/
```

For each command:

```text
Command:
What it does:
What output I expect:
What it proves:
What it does not prove:
```

# Completion checklist

```text
[ ] I can explain systemd.
[ ] I can explain start vs enable.
[ ] I can prove the app listens on port 8000.
[ ] I can test the backend directly.
[ ] I can read journal logs.
[ ] I stopped and restarted the service safely.
[ ] I updated COMMANDS_I_CAN_EXPLAIN.md.
```
