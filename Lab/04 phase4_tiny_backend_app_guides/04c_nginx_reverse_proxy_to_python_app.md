# 04c — Put Nginx in Front of the Tiny Python App

## Purpose

Now connect the pieces:

```text
browser or curl
→ Nginx on port 80
→ Python app on 127.0.0.1:8000
```

This is called a reverse proxy.

The goal is not deep Nginx.

The goal is to understand:

```text
Nginx receives the public HTTP request.
Nginx forwards the request to the local backend app.
The backend app returns a response.
Nginx returns that response to the client.
```

---

# Feynman version

```text
Nginx is the front desk.
The Python app is a worker in the back room.
Visitors talk to the front desk.
The front desk asks the back room and returns the answer.
```

---

# Part 1 — Confirm backend app is running

Run:

```bash
sudo systemctl status site4-app
curl http://127.0.0.1:8000/
sudo ss -ltnp | grep 8000
```

Write:

```text
Service status:

curl backend result:

Listening address and port:

What this proves:

What this does not prove:
```

Expected:

```text
This proves the backend works locally.
It does not prove Nginx can proxy to it.
```

---

# Part 2 — Create Nginx config

Create:

```bash
sudo vi /etc/nginx/conf.d/site4-app.conf
```

Use:

```nginx
server {
    listen 80;
    server_name site4.local;

    access_log /var/log/nginx/site4.access.log;
    error_log  /var/log/nginx/site4.error.log;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

## Stop and explain

Answer before testing:

```text
What port does Nginx listen on?

What hostname does this server block expect?

Where does Nginx forward requests?

Which log records requests to Nginx?

Which log records Nginx errors?
```

Green check:

```text
[ ] I know Nginx listens on port 80.
[ ] I know the backend app listens on 127.0.0.1:8000.
[ ] I know this config does not expose the Python app directly.
```

---

# Part 3 — Test and reload Nginx

Run:

```bash
sudo nginx -t
```

Only if successful:

```bash
sudo systemctl reload nginx
```

Write:

```text
What nginx -t proved:

What nginx -t did not prove:

Why reload was safe:
```

Expected:

```text
nginx -t proves config syntax is valid.
It does not prove the backend app works.
It does not prove curl will get the right response.
```

---

# Part 4 — Test through Nginx

From the development machine or app01:

```bash
curl -v -H 'Host: site4.local' http://app01/
```

Write:

```text
HTTP status:

Response body:

What this proves:

What this does not prove:
```

Expected:

```text
This proves Nginx accepted the request and returned a response from the backend.
It does not prove the backend is publicly exposed directly.
```

---

# Part 5 — Compare direct backend vs Nginx proxy

Direct backend test from app01:

```bash
curl http://127.0.0.1:8000/
```

Nginx proxy test:

```bash
curl -H 'Host: site4.local' http://app01/
```

Write:

```text
Direct backend result:

Nginx proxy result:

What is the same?

What is different?

Why test both?
```

Expected:

```text
Direct backend tests the app.
Nginx proxy test tests Nginx plus the app.
```

---

# Part 6 — Check logs

Access log:

```bash
sudo tail -n 20 /var/log/nginx/site4.access.log
```

Error log:

```bash
sudo tail -n 20 /var/log/nginx/site4.error.log
```

App logs:

```bash
sudo journalctl -u site4-app --since "10 minutes ago"
```

Write:

```text
Nginx access-log line:

Nginx error-log evidence:

App journal evidence:

What Nginx logs prove:

What app logs prove:
```

Expected distinction:

```text
Nginx access log proves the request reached Nginx.
Nginx error log helps explain proxy problems.
App journal shows the backend service output and failures.
```

---

# Final reflection

```text
Task:

Backend URL:

Public Nginx test command:

Nginx config file:

proxy_pass target:

Nginx access-log evidence:

Nginx error-log evidence:

App journal evidence:

What Nginx does:

What the Python app does:

One habit I practiced:
```

Completion checkpoint:

```text
[ ] I can test the backend directly.
[ ] I can test through Nginx.
[ ] I can explain proxy_pass.
[ ] I can explain access log vs app journal.
[ ] I can explain why Nginx reload does not prove the app works.
```
