# 055e — Break/Fix Lab for Tiny Form App

## Purpose

Troubleshoot the tiny form app with evidence.

Stack:

```text
browser/curl
→ Nginx
→ systemd service
→ app process
→ SQLite database
```

The important new failures involve:

```text
POST
redirect
database writes
permissions
```

---

# Before each exercise

Write:

```text
Failure I am creating:

Expected symptom:

Layer I expect to break:

Command to observe symptom:

Log to check:

How I will restore:
```

After each exercise:

```text
Exact symptom:

Exact evidence:

Broken layer:

Fix:

Verification:
```

---

# Exercise 1 — App service stopped

Break:

```bash
sudo systemctl stop site55-app
```

Test:

```bash
curl -v -H 'Host: site55.local' http://app01/
```

Check:

```bash
sudo systemctl status site55-app
sudo tail -n 20 /var/log/nginx/site55.error.log
```

Fix:

```bash
sudo systemctl start site55-app
```

Verify:

```bash
curl -v -H 'Host: site55.local' http://app01/
```

Write:

```text
HTTP symptom:

systemd evidence:

Nginx error evidence:

Broken layer:

Fix evidence:
```

---

# Exercise 2 — Wrong form action

Break locally or on a temporary copy:

Change form action from:

```html
<form method="POST" action="/messages">
```

to:

```html
<form method="POST" action="/wrong">
```

Deploy or run manually for testing.

Submit from curl:

```bash
curl -v -H 'Host: site55.local' -X POST -d 'body=wrong action test' http://app01/wrong
```

Expected:

```text
404 Not Found
```

Fix back:

```html
<form method="POST" action="/messages">
```

Restart/redeploy if needed.

Verify:

```bash
curl -v -H 'Host: site55.local' -X POST -d 'body=form action fixed' http://app01/messages
curl -s -H 'Host: site55.local' http://app01/ | grep 'form action fixed'
```

Write:

```text
Wrong action:

HTTP symptom:

Why /wrong failed:

Fix:

Verification:
```

---

# Exercise 3 — Database directory not writable

Back up first:

```bash
sudo systemctl stop site55-app
sudo -u site55 sh -c '
mkdir -p /var/backups/site55-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site55-app/site55.db /var/backups/site55-app/site55-before-permission-test-$timestamp.db
ls -lh /var/backups/site55-app/site55-before-permission-test-$timestamp.db
'
sudo systemctl start site55-app
```

Break:

```bash
sudo chown root:root /var/lib/site55-app
```

Restart:

```bash
sudo systemctl restart site55-app
```

Test POST:

```bash
curl -v -H 'Host: site55.local' -X POST -d 'body=permission failure' http://app01/messages
```

Check:

```bash
sudo journalctl -u site55-app --since "10 minutes ago"
ls -ld /var/lib/site55-app
ls -l /var/lib/site55-app/site55.db
```

Fix:

```bash
sudo chown -R site55:site55 /var/lib/site55-app
sudo systemctl restart site55-app
```

Verify:

```bash
curl -H 'Host: site55.local' -X POST -d 'body=permission fixed' http://app01/messages
curl -s -H 'Host: site55.local' http://app01/ | grep 'permission fixed'
```

Write:

```text
Permission break:

POST symptom:

Journal evidence:

Ownership evidence:

Fix:

Verification:
```

---

# Exercise 4 — Wrong database path in systemd

Break:

```bash
sudo vi /etc/systemd/system/site55-app.service
```

Change:

```ini
Environment=SITE55_DB=/var/lib/site55-app/site55.db
```

to:

```ini
Environment=SITE55_DB=/var/lib/site55-app/mistake/site55.db
```

Reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart site55-app
sudo systemctl status site55-app
```

Check:

```bash
sudo journalctl -u site55-app --since "10 minutes ago"
```

Fix back:

```ini
Environment=SITE55_DB=/var/lib/site55-app/site55.db
```

Reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart site55-app
```

Verify old messages:

```bash
curl -s -H 'Host: site55.local' http://app01/
```

Write:

```text
Wrong DB path:

Service symptom:

Journal evidence:

Fix:

Did old messages return?

What this proves:
```

---

# Exercise 5 — Duplicate submission thought experiment

Submit a message:

```bash
curl -v -H 'Host: site55.local' -X POST -d 'body=redirect test message' http://app01/messages
```

Observe:

```text
303 See Other
Location: /
```

Now check page:

```bash
curl -s -H 'Host: site55.local' http://app01/ | grep 'redirect test message'
```

Answer:

```text
Why does the server return 303?

What would be the risk if refresh repeated POST?

How does redirect-after-POST help?
```

---

# Final reflection

```text
Most useful curl command:

Most useful systemd command:

Most useful Nginx log:

Most useful app log:

Most useful permission command:

One problem caused by Nginx:

One problem caused by app code:

One problem caused by database permissions:

One problem caused by systemd configuration:

My troubleshooting order next time:
1.
2.
3.
4.
5.
```

Completion checkpoint:

```text
[ ] I can diagnose app service stopped.
[ ] I can diagnose wrong form action.
[ ] I can diagnose database permission failure.
[ ] I can diagnose wrong database path.
[ ] I can explain redirect-after-POST.
[ ] I can use evidence instead of guessing.
```
