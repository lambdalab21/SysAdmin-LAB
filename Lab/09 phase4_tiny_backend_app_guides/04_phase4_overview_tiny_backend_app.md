# Phase 4 Overview — Tiny Backend App Behind Nginx

## Purpose

Until now, you deployed static files:

```text
browser
  → Nginx
  → file on disk
```

Now you will deploy a tiny backend app:

```text
browser
  → Nginx
  → Python app running on 127.0.0.1:8000
```

The goal is not to become a Python web developer.

The goal is to understand this server-side shape:

```text
process
→ port
→ curl
→ systemd
→ Nginx reverse proxy
→ logs
→ troubleshooting
```

---

# What you are learning

A web app can be:

```text
a running process that listens on a port
```

If the process stops, the app is down.

If the process listens on the wrong address or port, Nginx cannot reach it.

If Nginx points to the wrong port, users get an error.

---

# Feynman version

Explain it to a younger student:

```text
A static site is like a book sitting on a shelf.
Nginx picks up the book and shows the page.

A backend app is like a person sitting in a room.
Nginx asks the person a question.
The person answers.
If the person is asleep, missing, or in the wrong room, Nginx cannot get the answer.
```

In this lab:

```text
The Python app is the person.
Port 8000 is the room number.
Nginx is the messenger.
curl is how we test the messenger and the person.
logs are the notebook showing what happened.
```

---

# What not to focus on yet

Do not go deep into:

```text
Python classes
self
HTTPServer internals
BaseHTTPRequestHandler internals
bytes vs strings
Flask
WSGI
ASGI
production Python app servers
```

Those are later topics.

For now, understand only this:

```text
This Python script starts a process.
The process listens on 127.0.0.1:8000.
When curl sends a GET request, the script returns text.
```

---

# Phase 4 files

Use these in order:

```text
04a_tiny_python_app_manual_run.md
04b_tiny_python_app_systemd_service.md
04c_nginx_reverse_proxy_to_python_app.md
04d_break_fix_tiny_app_nginx.md
```

Recommended pace:

```text
Day 1: 04a
Day 2: 04b
Day 3: 04c
Day 4: 04d
```

Do not rush. The value is in writing evidence, not finishing quickly.

---

# Before every important command

Write:

```text
Question:
Command:
Expected evidence:
Risk if I am wrong:
```

After the command, write:

```text
Exact output:
What this proves:
What this does not prove:
Next narrow question:
```

Do not write only:

```text
worked
failed
done
probably
```

Those are not evidence.

---

# Completion standard for Phase 4

You are done when you can explain without notes:

```text
A backend app is a running process.
The process listens on a port.
127.0.0.1 means localhost.
curl can test the app directly.
systemd can manage the app process.
Nginx can forward requests to the app.
Nginx access logs show requests to Nginx.
Nginx error logs help explain proxy failures.
journalctl -u app-service shows app service logs.
A 502 from Nginx often means Nginx reached the proxy layer but could not get a good response from the backend.
```
