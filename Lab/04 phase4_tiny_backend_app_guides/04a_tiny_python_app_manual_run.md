# 04a — Tiny Python App: Manual Run

---

# Mental model

```text
Python script = program file
Running Python script = process
127.0.0.1 = this machine only
8000 = port number
curl = test client
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

Answer these:

```text
What address will the app listen on? 172.0.0.1

What port will it listen on? 8000. 

What text should it return? 'Hello' from your tiny Python server.

Will this app be reachable directly from another machine? Why or why not? No, it binds only to 127.0.0.1.
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
What process is running? The Python HTTP server process. 

Why did the prompt not come back? `serve_forever()` runs an infinite loop. 

What would happen if I press Ctrl-C? It stops the server. 
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
HTTP status: 200. 

Response body: Hello from your tiny python server

What this proves: The app works locally on app01

What this does not prove: Reachability from other machines or nginx integration. 
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
What changed in the code? The response text. 

What changed in curl output? The message body. 

What stayed the same? Address(127.0.0.1), the port(8000), and overall behavior.
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
Command that started the process: python3 app.py

Command that tested the app: curl http://127.0.0.1:8000/

Command that proved the port was listening: ss -ltnp | grep 8000

What 127.0.0.1 means: localhost.

What port 8000 means: The specific port the app listens on.

What happens when the process stops: The port becomes free and connections fail. 

One thing I did not need to understand deeply yet: Production deployment

One habit I practiced: Testing in small steps and verifying with tools like curl and ss. 
```
