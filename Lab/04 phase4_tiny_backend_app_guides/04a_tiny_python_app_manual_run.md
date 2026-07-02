# 04a — Tiny Python App: Manual Run

## Purpose

Start a tiny backend app manually and prove it is listening on a port.

The goal is not Python.

The goal is:

```text
process
→ listens on 127.0.0.1:8000
→ curl reaches it
→ ss proves the listening socket
→ stopping the process makes the app unavailable
```

---

# Mental model

```text
Python script = program file
Running Python script = process
127.0.0.1 = this machine only
8000 = port number
curl = test client
```

Feynman version:

```text
The app is like a person sitting in room 8000 on app01.
curl knocks on room 8000 and reads the answer.
```

---

# Part 1 — Create the app file

On `app01`, create a project directory:

```bash
mkdir -p ~/projects/site4-app
cd ~/projects/site4-app
vi app.py
```

Put this in `app.py`:

```python
from http.server import HTTPServer, BaseHTTPRequestHandler

class SimpleHTTPRequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-Type", "text/plain")
        self.end_headers()
        self.wfile.write(b"Hello from your tiny Python server!\n")

server_address = ("127.0.0.1", 8000)
httpd = HTTPServer(server_address, SimpleHTTPRequestHandler)

print("Server running on http://127.0.0.1:8000 ...")
httpd.serve_forever()
```

## Stop and think

Do not study every Python detail.

Answer only these:

```text
What address will the app listen on?

What port will it listen on?

What text should it return?

Will this app be reachable directly from another machine? Why or why not?
```

Expected:

```text
It listens on 127.0.0.1 port 8000.
It returns a plain text message.
127.0.0.1 means localhost, so it is only reachable from app01 itself.
```

Green check:

```text
[ ] I know the address.
[ ] I know the port.
[ ] I know this is a running process, not a static file.
```

---

# Part 2 — Run the app manually

Run:

```bash
python3 app.py
```

Leave it running.

You should see:

```text
Server running on http://127.0.0.1:8000 ...
```

## Stop and think

This terminal is now occupied because the process is running.

Write:

```text
What process is running?

Why did the prompt not come back?

What would happen if I press Ctrl-C?
```

Expected:

```text
The Python process is running and waiting for requests.
The prompt did not return because serve_forever() keeps the server alive.
Ctrl-C would stop the process.
```

---

# Part 3 — Test with curl from another terminal

Open another terminal or SSH session to `app01`.

Run:

```bash
curl -v http://127.0.0.1:8000/
```

Write exact evidence:

```text
HTTP status:

Response body:

What this proves:

What this does not prove:
```

Expected:

```text
This proves the app responds locally on app01.
It does not prove Nginx can reach it.
It does not prove other machines can reach it directly.
It does not prove systemd manages it.
```

---

# Part 4 — Prove the process is listening

Run:

```bash
ss -ltnp | grep 8000
```

If permission hides the process name, try:

```bash
sudo ss -ltnp | grep 8000
```

Write:

```text
Exact output:

Listening address:

Listening port:

Process name or PID:

What this proves:
```

Expected idea:

```text
The output should show 127.0.0.1:8000.
This proves a process is listening locally on port 8000.
```

Do not continue if the output shows:

```text
0.0.0.0:8000
```

That means all interfaces, not localhost only.

---

# Part 5 — Stop the app and test failure

Go back to the terminal running `python3 app.py`.

Press:

```text
Ctrl-C
```

Now run:

```bash
curl -v http://127.0.0.1:8000/
```

Write:

```text
What changed?

What curl error did I get?

What does this prove?
```

Expected:

```text
The app process stopped.
No process is listening on port 8000.
curl cannot connect.
```

---

# Part 6 — Small modification test

Edit `app.py`:

```bash
vi app.py
```

Change only the response text:

```python
self.wfile.write(b"Hello from app01 tiny app!\n")
```

Run again:

```bash
python3 app.py
```

From another terminal:

```bash
curl http://127.0.0.1:8000/
```

Write:

```text
What changed in the code?

What changed in curl output?

What stayed the same?
```

Expected:

```text
Only the response body changed.
The address and port stayed the same.
The process still listens on 127.0.0.1:8000.
```

---

# Final reflection

```text
Task:

Command that started the process:

Command that tested the app:

Command that proved the port was listening:

What 127.0.0.1 means:

What port 8000 means:

What happens when the process stops:

One thing I did not need to understand deeply yet:

One habit I practiced:
```

Completion checkpoint:

```text
[ ] I can run the app manually.
[ ] I can test it with curl.
[ ] I can prove it is listening with ss.
[ ] I can explain why it stops when I press Ctrl-C.
[ ] I understand why systemd is the next step.
```
