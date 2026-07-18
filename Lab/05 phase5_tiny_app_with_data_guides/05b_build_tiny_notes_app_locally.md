#LAB #deployment #backend
# 05b — Build the Tiny Notes App Locally

## Purpose

Create a tiny backend app that stores notes in SQLite.

The goal is not advanced Python.

The goal is to understand:

```text
GET / reads data and shows a page
POST /notes writes data
SQLite stores data in a file
restart does not erase data
```

---

# Part 1 — Create local project

```bash
mkdir -p ~/projects/site5-app/data
cd ~/projects/site5-app
vi app.py
```

Use this app:

```python
from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import parse_qs
import html
import os
import sqlite3

DB_PATH = os.environ.get("SITE5_DB", "./data/site5.db")

def init_db():
    os.makedirs(os.path.dirname(DB_PATH), exist_ok=True)
    with sqlite3.connect(DB_PATH) as conn:
        conn.execute("""
            CREATE TABLE IF NOT EXISTS notes (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                body TEXT NOT NULL
            )
        """)

def add_note(body):
    with sqlite3.connect(DB_PATH) as conn:
        conn.execute("INSERT INTO notes (body) VALUES (?)", (body,))

def get_notes():
    with sqlite3.connect(DB_PATH) as conn:
        return conn.execute("SELECT id, body FROM notes ORDER BY id DESC").fetchall()

class NotesHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path != "/":
            self.send_response(404)
            self.end_headers()
            self.wfile.write(b"Not found
")
            return

        notes = get_notes()
        items = "
".join(
            f"<li>{note_id}: {html.escape(body)}</li>"
            for note_id, body in notes
        )

        page = f"""<!doctype html>
<html>
<head><title>site5 notes</title></head>
<body>
<h1>site5 notes</h1>
<form method="POST" action="/notes">
  <input name="body">
  <button type="submit">Add note</button>
</form>
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
        if self.path != "/notes":
            self.send_response(404)
            self.end_headers()
            self.wfile.write(b"Not found
")
            return

        length = int(self.headers.get("Content-Length", "0"))
        raw_body = self.rfile.read(length).decode("utf-8")
        form = parse_qs(raw_body)
        note = form.get("body", [""])[0].strip()

        if note:
            add_note(note)

        self.send_response(303)
        self.send_header("Location", "/")
        self.end_headers()

if __name__ == "__main__":
    init_db()
    server_address = ("127.0.0.1", 8000)
    httpd = HTTPServer(server_address, NotesHandler)
    print(f"site5 app running on http://127.0.0.1:8000 using DB {DB_PATH}")
    httpd.serve_forever()
```

---

# Part 2 — Understand only what is needed

Do not chase every Python detail.

Understand:

```text
DB_PATH tells where the database file lives.
init_db creates the table if needed.
GET / shows notes.
POST /notes adds a note.
The app listens on 127.0.0.1:8000.
```

Stop and answer:

```text
What path shows notes? GET /(The root path)
What path adds a note? POST /notes. 
What does 127.0.0.1 mean? This is the localhost 
```

---

# Part 3 — Run and test GET

Run:

```bash
python3 app.py
```

In another terminal:

```bash
curl -v http://127.0.0.1:8000/
```

Write:

```text
HTTP status: 200 ok. 
Content-Type: text/html; charset = utf-8
What this proves: The server responds to get GET requests and serves an HTML page with the notes list. 
What this does not prove: That POST works or that data persists across restarts.
```

---

# Part 4 — Test POST

```bash
curl -v -X POST -d 'body=first note from curl' http://127.0.0.1:8000/notes
curl -s http://127.0.0.1:8000/ | grep 'first note from curl'
```

Write:

```text
POST status: 303 See other
GET evidence after POST: The note text appears in the <UL> list. 
What this proves: Data can be written via POST and immediately read back on the next GET. 
```

---

# Part 5 — Prove database exists

```bash
find ./data -type f -print -ls
```

Optional if installed:

```bash
sqlite3 ./data/site5.db '.tables'
sqlite3 ./data/site5.db 'SELECT id, body FROM notes;'
```

Write:

```text
Database file: ./data/site5.db
File size: Non-zero. 
What this proves: Notes are persisted to a real SQLite file on the disk, not just memory. 
```

---

# Part 6 — Stop and restart

Stop with `Ctrl-C`, then run again:

```bash
python3 app.py
curl -s http://127.0.0.1:8000/ | grep 'first note from curl'
```

---

# Final reflection

```text
Database path: ./data/site5.db
Command that tested GET: curl -v http://127.0.0.1:8000/
Command that tested POST: curl -v -X POST -d 'body=first note from curl' http://127.0
Command that proved the database file exists: find ./data -type f -print ls. 
What survived restart: The notes stored in the database. 
Why this matters for deployment: Real web apps need persistent storage, so data doesn't vanish when the server restarts or crashes. 
One Python detail I intentionally did not chase deeply: The full http.server / BaseHTTPRequestHandler internals or advanced splite3 features. 
```
