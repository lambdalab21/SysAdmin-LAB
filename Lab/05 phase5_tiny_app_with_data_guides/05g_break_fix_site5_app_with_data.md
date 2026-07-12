#LAB #deployment #backend
# 05g — Break/Fix Lab: `site5-app` with Data

## Purpose

Create controlled failures and diagnose them with evidence.

Stack:

```text
browser/curl → Nginx → systemd service → Python app → SQLite database
```

New layer:

```text
data and permissions
```

---

# Before each exercise

Write:

```text
Failure I am creating:
Layer I expect to break:
Expected symptom:
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

# Exercise 1 — Stop the app service

Break:

```bash
sudo systemctl stop site5-app
```

Test:

```bash
curl -v -H 'Host: site5.local' http://app01/
```

Check:

```bash
sudo systemctl status site5-app
sudo tail -n 20 /var/log/nginx/site5.error.log
```

Fix:

```bash
sudo systemctl start site5-app
curl -v -H 'Host: site5.local' http://app01/
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

# Exercise 2 — Wrong Nginx proxy port

Break:

```bash
sudo vi /etc/nginx/conf.d/site5-app.conf
```

Change temporarily:

```nginx
proxy_pass http://127.0.0.1:8000;
```

to:

```nginx
proxy_pass http://127.0.0.1:8999;
```

Reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Test and check:

```bash
curl -v -H 'Host: site5.local' http://app01/
sudo ss -ltnp | grep 8000
sudo ss -ltnp | grep 8999 || echo 'nothing listening on 8999'
sudo tail -n 20 /var/log/nginx/site5.error.log
```

Fix back to 8000, reload, verify.

Write:

```text
Symptom:
Evidence app still listened on 8000:
Evidence nothing listened on 8999:
Broken layer:
Fix:
```

---

# Exercise 3 — Database directory not writable

Back up first:

```bash
sudo systemctl stop site5-app
sudo -u site5 sh -c '
mkdir -p /var/backups/site5-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site5-app/site5.db /var/backups/site5-app/site5-before-permission-test-$timestamp.db
ls -lh /var/backups/site5-app/site5-before-permission-test-$timestamp.db
'
sudo systemctl start site5-app
```

Break ownership:

```bash
sudo chown root:root /var/lib/site5-app
sudo systemctl restart site5-app
```

Test:

```bash
curl -v -H 'Host: site5.local' http://app01/
curl -v -H 'Host: site5.local' -X POST -d 'body=permission failure note' http://app01/notes
```

Check:

```bash
sudo journalctl -u site5-app --since '10 minutes ago'
ls -ld /var/lib/site5-app
ls -l /var/lib/site5-app/site5.db
```

Fix:

```bash
sudo chown -R site5:site5 /var/lib/site5-app
sudo systemctl restart site5-app
curl -H 'Host: site5.local' -X POST -d 'body=permission fixed note' http://app01/notes
curl -s -H 'Host: site5.local' http://app01/ | grep 'permission fixed note'
```

Write:

```text
Permission change made:
GET result:
POST result:
Journal evidence:
Ownership evidence:
Fix:
Verification:
```

---

# Exercise 4 — Wrong database path

Break systemd environment:

```bash
sudo vi /etc/systemd/system/site5-app.service
```

Temporarily change:

```ini
Environment=SITE5_DB=/var/lib/site5-app/site5.db
```

to:

```ini
Environment=SITE5_DB=/var/lib/site5-app/mistake/site5.db
```

Reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart site5-app
sudo systemctl status site5-app
sudo journalctl -u site5-app --since '10 minutes ago'
```

Fix path back, reload, restart, verify old data.

Write:

```text
Wrong path used:
systemd result:
journal evidence:
Fix:
Did old data return?
What this proves:
```

---

# Exercise 5 — Data survival after restart

```bash
curl -H 'Host: site5.local' -X POST -d 'body=restart survival note' http://app01/notes
curl -s -H 'Host: site5.local' http://app01/ | grep 'restart survival note'
sudo systemctl restart site5-app
curl -s -H 'Host: site5.local' http://app01/ | grep 'restart survival note'
```

Write:

```text
Before restart evidence:
After restart evidence:
What this proves:
```

---

# Final troubleshooting reflection

```text
Most useful command:
Most useful log:
Most useful permission command:
One problem caused by Nginx:
One problem caused by systemd/app service:
One problem caused by database permissions:
One problem caused by wrong configuration:
My troubleshooting order next time:
1.
2.
3.
4.
5.
```
