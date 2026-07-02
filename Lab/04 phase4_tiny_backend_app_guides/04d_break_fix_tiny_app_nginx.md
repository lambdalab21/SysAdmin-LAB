# 04d — Break/Fix Lab: Tiny App + Nginx

## Purpose

This lab teaches troubleshooting.

You will create controlled failures and prove what broke using evidence.

The goal is:

```text
symptom
→ exact evidence
→ layer
→ fix
→ verification
```

Do not guess.

---

# Layers

```text
client curl
→ Nginx
→ proxy_pass
→ backend port
→ Python app process
→ systemd service
```

A good troubleshooter asks:

```text
Which layer failed?
What evidence proves it?
```

---

# Before each exercise

Write:

```text
Failure I am creating:

Expected symptom:

Command to observe symptom:

Log I will check:

How I will restore:
```

After each exercise:

```text
Exact symptom:

Exact log evidence:

Broken layer:

Fix command:

Verification:
```

---

# Exercise 1 — Stop the backend app

Create failure:

```bash
sudo systemctl stop site4-app
```

Test through Nginx:

```bash
curl -v -H 'Host: site4.local' http://app01/
```

Check logs:

```bash
sudo tail -n 20 /var/log/nginx/site4.error.log
sudo journalctl -u site4-app --since "10 minutes ago"
```

Fix:

```bash
sudo systemctl start site4-app
```

Verify:

```bash
curl -v -H 'Host: site4.local' http://app01/
```

Write:

```text
HTTP symptom:

Nginx error-log line:

systemd status:

Broken layer:

Fix:

Verification:
```

Expected idea:

```text
Nginx was running, but the backend app was unavailable.
```

---

# Exercise 2 — Wrong proxy port

Edit Nginx config:

```bash
sudo vi /etc/nginx/conf.d/site4-app.conf
```

Temporarily change:

```nginx
proxy_pass http://127.0.0.1:8000;
```

to:

```nginx
proxy_pass http://127.0.0.1:8999;
```

Test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Test:

```bash
curl -v -H 'Host: site4.local' http://app01/
```

Check:

```bash
sudo tail -n 20 /var/log/nginx/site4.error.log
sudo ss -ltnp | grep 8000
sudo ss -ltnp | grep 8999 || echo "nothing listening on 8999"
```

Fix config back to 8000:

```nginx
proxy_pass http://127.0.0.1:8000;
```

Reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Verify:

```bash
curl -v -H 'Host: site4.local' http://app01/
```

Write:

```text
HTTP symptom:

Evidence that app still listened on 8000:

Evidence nothing listened on 8999:

Broken layer:

Fix:

Verification:
```

Expected idea:

```text
The app was alive.
Nginx was pointing to the wrong backend port.
```

---

# Exercise 3 — Bad Python code

Edit app:

```bash
vi ~/projects/site4-app/app.py
```

Create a simple syntax error, such as removing a closing parenthesis.

Restart:

```bash
sudo systemctl restart site4-app
sudo systemctl status site4-app
```

Check logs:

```bash
sudo journalctl -u site4-app --since "5 minutes ago"
```

Fix the syntax error.

Restart:

```bash
sudo systemctl restart site4-app
```

Verify directly:

```bash
curl http://127.0.0.1:8000/
```

Verify through Nginx:

```bash
curl -H 'Host: site4.local' http://app01/
```

Write:

```text
systemd symptom:

journalctl error:

Broken layer:

Fix:

Verification:
```

Expected idea:

```text
Nginx may be fine.
The app service failed because the Python program could not start.
```

---

# Exercise 4 — Nginx config syntax error

Edit config:

```bash
sudo vi /etc/nginx/conf.d/site4-app.conf
```

Create a small syntax error, such as removing a semicolon.

Test:

```bash
sudo nginx -t
```

Do not reload if the test fails.

Fix the config.

Test again:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Verify:

```bash
curl -H 'Host: site4.local' http://app01/
```

Write:

```text
nginx -t error:

Why reload was not safe:

Fix:

Verification:
```

Expected idea:

```text
nginx -t protects you from loading a broken Nginx config.
```

---

# Final troubleshooting reflection

```text
Most useful command:

Most useful log:

One wrong assumption I avoided:

One failure that looked like Nginx but was actually the app:

One failure that looked like the app but was actually Nginx config:

My troubleshooting order next time:
1.
2.
3.
```

Completion checkpoint:

```text
[ ] I can identify backend-down symptoms.
[ ] I can identify wrong proxy port symptoms.
[ ] I can identify app startup failure.
[ ] I can identify Nginx config syntax failure.
[ ] I can use systemctl, journalctl, curl, ss, and Nginx logs together.
```
