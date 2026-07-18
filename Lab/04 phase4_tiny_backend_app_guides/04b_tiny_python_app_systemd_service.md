#LAB #deployment #backend
# 04b — Manage the Tiny Python App with systemd

## Purpose

In the previous lab, the app worked only while a terminal was running:

```text
python3 app.py
```

Now make Linux manage the app as a service.

The goal is:

```text
manual process
→ systemd service
→ start/stop/status
→ journalctl logs
```

---

# Mental model

Feynman version:

```text
Running the app manually is like babysitting it yourself.
systemd is like hiring a supervisor.
The supervisor can start the app, stop it, report whether it is running, and record its output.
```

---

# Part 1 — Confirm the app works manually

Before writing a service, confirm the app still works.

```bash
cd ~/projects/site4-app
python3 app.py
```

From another terminal:

```bash
curl http://127.0.0.1:8000/
```

Stop the app with:

```text
Ctrl-C
```

Write:

```text
Manual app test result:

What this proves:

What this does not prove:
```

Expected:

```text
It proves the app code works.
It does not prove systemd can run it.
```

---

# Part 2 — Find exact paths

Run:

```bash
pwd
which python3
ls -l app.py
```

Write:

```text
App directory:

Python path:

App file:

User who should run the app:
```

Use a normal user, not root, if possible.

Example:

```text
App directory: /home/deploy/projects/site4-app
Python path: /usr/bin/python3
App file: /home/deploy/projects/site4-app/app.py
User: deploy
```

## Stop and think

A systemd service needs exact paths.

Do not write:

```text
the app folder
python
the file
```

Write exact paths.

Green check:

```text
[ ] I know the exact app directory.
[ ] I know the exact Python path.
[ ] I know which user runs the service.
```

---

# Part 3 — Create the service file

Create:

```bash
sudo vi /etc/systemd/system/site4-app.service
```

Example:

```ini
[Unit]
Description=Tiny Python site4 app
After=network.target

[Service]
Type=simple
User=deploy
WorkingDirectory=/home/deploy/projects/site4-app
ExecStart=/usr/bin/python3 /home/deploy/projects/site4-app/app.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Adjust paths and user.

## Stop and explain

Before starting the service, answer:

```text
What user runs the app?

What directory does it run from?

What command starts it?

What does Restart=on-failure mean?
```

---

# Part 4 — Load and start the service

Run:

```bash
sudo systemctl daemon-reload
sudo systemctl start site4-app
sudo systemctl status site4-app
```

Write:

```text
Service state:

Main PID:

What status output proves:

What status output does not prove:
```

Expected:

```text
systemctl status proves systemd started the service.
It does not prove the app returns the correct HTTP response.
```

---

# Part 5 — Test the service with curl

Run:

```bash
curl -v http://127.0.0.1:8000/
```

Check listening port:

```bash
sudo ss -ltnp | grep 8000
```

Write:

```text
curl evidence:

ss evidence:

What this proves:
```

---

# Part 6 — Read service logs

Run:

```bash
sudo journalctl -u site4-app --since "10 minutes ago"
```

Follow logs live:

```bash
sudo journalctl -u site4-app -f
```

From another terminal:

```bash
curl http://127.0.0.1:8000/
```

Stop log follow with:

```text
Ctrl-C
```

Write:

```text
Exact journal line:

What journalctl proves:

What it does not prove:
```

---

# Part 7 — Stop and restart

Stop:

```bash
sudo systemctl stop site4-app
```

Test:

```bash
curl -v http://127.0.0.1:8000/
```

Start again:

```bash
sudo systemctl start site4-app
curl http://127.0.0.1:8000/
```

Write:

```text
What happened when stopped?

What happened after start?

What does systemd control?
```

---

# Part 8 — Enable only after understanding

Enable at boot:

```bash
sudo systemctl enable site4-app
```

Check:

```bash
systemctl is-enabled site4-app
```

Stop and answer:

```text
What does start do?

What does enable do?

Are they the same?
```

Expected:

```text
start runs the service now.
enable makes it start automatically at boot.
```

---

# Final reflection

```text
Task:

Service name:

User running the service:

ExecStart command:

Command that started it:

Command that checked status:

Command that showed logs:

Command that proved HTTP worked:

What systemd does:

What systemd does not prove by itself:

One habit I practiced:
```

Completion checkpoint:

```text
[ ] I can explain systemd start/stop/status.
[ ] I can explain start vs enable.
[ ] I can read journal logs for the app.
[ ] I can prove the app is listening.
[ ] I can prove the app responds with curl.
```
