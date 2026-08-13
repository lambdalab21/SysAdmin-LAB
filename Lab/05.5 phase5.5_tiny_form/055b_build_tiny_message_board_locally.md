# 055b — Build Tiny Message Board Locally

## Purpose

Build a tiny app that receives form input and stores messages in SQLite.

Features:

```text
GET /              show page, form, and messages
POST /messages     save one message, then redirect to /
```
---

# Part 1 — Create project

```bash
mkdir -p ~/projects/site55-message-board/data
cd ~/projects/site55-message-board
vi app.py
```

Put this in `app.py`:

```python
from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import parse_qs
from datetime import datetime, timezone
import html
import os
import sqlite3

DB_PATH = os.environ.get("SITE55_DB", "./data/site55.db")

def init_db():
    os.makedirs(os.path.dirname(DB_PATH), exist_ok=True)
    with sqlite3.connect(DB_PATH) as conn:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS messages (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                body TEXT NOT NULL,
                created_at TEXT NOT NULL
            )
        """)

def add_message(body):
    now = datetime.now(timezone.utc).isoformat()
    with sqlite3.connect(DB_PATH) as conn:
        conn.execute(
            "INSERT INTO messages (body, created_at) VALUES (?, ?)",
            (body, now)
        )

def get_messages():
    with sqlite3.connect(DB_PATH) as conn:
        return conn.execute(
            "SELECT id, body, created_at FROM messages ORDER BY id DESC"
        ).fetchall()

class MessageBoardHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path != "/":
            self.send_response(404)
            self.end_headers()
            self.wfile.write(b"Not found\n")
            return

        messages = get_messages()
        items = "\n".join(
            f"<li><strong>{msg_id}</strong>: {html.escape(body)} <small>{html.escape(created_at)}</small></li>"
            for msg_id, body, created_at in messages
        )

        page = f"""<!doctype html>
<html>
<head>
  <title>site55 message board</title>
</head>
<body>
  <h1>site55 message board</h1>

  <form method="POST" action="/messages">
    <input name="body" maxlength="200">
    <button type="submit">Add message</button>
  </form>

  <h2>Messages</h2>
  <ul>
    {items}
  </ul>
</body>
</html>
"""
        body = page.encode("utf-8")
        self.send_response(200)
        self.send_header("Content-Type", "text/html; charset=utf-8")
        self.send_header("Content-Length", str(len(body)))
        self.end_headers()
        self.wfile.write(body)

    def do_POST(self):
        if self.path != "/messages":
            self.send_response(404)
            self.end_headers()
            self.wfile.write(b"Not found\n")
            return

        length = int(self.headers.get("Content-Length", "0"))
        raw_body = self.rfile.read(length).decode("utf-8")
        form = parse_qs(raw_body)
        message = form.get("body", [""])[0].strip()

        if 0 < len(message) <= 200:
            add_message(message)

        self.send_response(303)
        self.send_header("Location", "/")
        self.end_headers()

if __name__ == "__main__":
    init_db()
    server_address = ("127.0.0.1", 8000)
    httpd = HTTPServer(server_address, MessageBoardHandler)
    print(f"site55 running at http://127.0.0.1:8000 using DB {DB_PATH}")
    httpd.serve_forever()
```

---

# Part 2 — Understand only what matters

Do not go deep into every Python line.

Understand:

```text
DB_PATH says where the database file is.
GET / shows the form and messages.
POST /messages receives the form.
add_message writes to SQLite.
303 redirect sends the browser back to /.
The app listens on 127.0.0.1:8000.
```

```text
Where is the local database? It's at ./data/site55.db. 

What path shows the page? / shows the page. 

What path receives the form? /messages receives the form. 

What field name holds the message? body field holds the message. 

	What happens after POST succeeds? After a successful POST, it issues a 303 redirect back to /. 
```

---

# Part 3 — Run locally

```bash
python3 app.py
```

In another terminal:

```bash
curl -v http://127.0.0.1:8000/
```

Write:

```text
HTTP status: 200

Content-Type: text/html: charset=utf-8

Page title or heading found: site55 message board

What this proves: It proves that the server responds to GET /. 

What this does not prove: This does not prove form submsision or database persistence. 
```

---

# Part 4 — Submit a message with curl

Run:

```bash
curl -v -X POST -d 'body=hello from curl' http://127.0.0.1:8000/messages
```

Notice the status should be:

```text
303 See Other
```

Then run:

```bash
curl -s http://127.0.0.1:8000/ | grep 'hello from curl'
```

Write:

```text
POST status: 303 See Other. 

Redirect Location: /

GET evidence after POST: TThe message text appears in the list. 

What this proves: This proves the message was saved and redirect works. 
```

---

# Part 5 — Submit from browser

Open:

```text
http://127.0.0.1:8000/
```

Add a message using the form.

Then verify with curl:

```bash
curl -s http://127.0.0.1:8000/ | grep 'your message text'
```

Write:

```text
Browser message: Successfully added via form. 

curl evidence: Message text appears in HTML. 

What this proves: This proves browser from submission works.

What this does not prove: This does not prove persistence across server starts.
```

---

# Part 6 — Prove persistence

Find database:

```bash
find ./data -type f -print -ls
```

Stop the app:

```text
Ctrl-C
```

Start again:

```bash
python3 app.py
```

Check old message:

```bash
curl -s http://127.0.0.1:8000/ | grep 'hello from curl'
```

Write:

```text
Database file: ./data/site55.db

Did message survive restart? Yes, the message survived. 

What this proves: Data is stored in SQLite. 
```

Expected:

```text
The message was stored in SQLite, not only memory.
```

---

# Part 7 — Think about validation

The app ignores:

```text
empty messages
messages longer than 200 characters
```

Answer:

```text
Why reject empty messages? Reject empty messages to avoid clutter and meaningless entries. 

Why limit length? To keep the UI clean and prevent any abuse. 

Does this make the app secure? no

What security questions are not covered yet? XSS, SQL injection, rate limiting, and authentication. 
```

---

# Final reflection

```text
Task: Build a tiny persistent message board. 

GET path: /

POST path: /messages

Form field name: body

Database file: ./data/site55.db

Command that proved GET: curl http://127.0.0.1:8000/

Command that proved POST: curl -X POST -d 'body=...' http://127.0.0.1:8000/messages

Command that proved redirect: 303 status + location header. 

Command that proved persistence: restart server, then curl GET. 

One Python detail I intentionally did not chase deeply: Full HTTP server class internals or advanced sqlite3 features. 
```
