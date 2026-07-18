# 06b — Build Flask + Jinja CRUD App Locally

## Purpose

Build a small CRUD app locally.

The app is a task tracker.

Features:

```text
list tasks
create task
view task
edit task
delete task
```

The deeper purpose:

```text
understand routes, templates, database writes, redirects, and persistence
```

---

# Part 1 — Create project

```bash
mkdir -p ~/projects/site6-crud/templates ~/projects/site6-crud/data
cd ~/projects/site6-crud
```

Create a virtual environment:

```bash
python3 -m venv .venv
. .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install Flask
```

Record:

```text
Python version:

Flask installed:

Virtual environment path:

What this proves:
```

---

# Part 2 — Create `app.py`

```bash
vi app.py
```

Use:

```python
from flask import Flask, g, redirect, render_template, request, url_for, abort
import os
import sqlite3

app = Flask(__name__)

DB_PATH = os.environ.get("SITE6_DB", "./data/site6.db")

def get_db():
    if "db" not in g:
        os.makedirs(os.path.dirname(DB_PATH), exist_ok=True)
        g.db = sqlite3.connect(DB_PATH)
        g.db.row_factory = sqlite3.Row
    return g.db

@app.teardown_appcontext
def close_db(error):
    db = g.pop("db", None)
    if db is not None:
        db.close()

def init_db():
    db = get_db()
    db.execute("""
        CREATE TABLE IF NOT EXISTS tasks (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            body TEXT NOT NULL DEFAULT '',
            done INTEGER NOT NULL DEFAULT 0
        )
    """)
    db.commit()

def get_task(task_id):
    task = get_db().execute(
        "SELECT id, title, body, done FROM tasks WHERE id = ?",
        (task_id,)
    ).fetchone()
    if task is None:
        abort(404)
    return task

@app.before_request
def ensure_db():
    init_db()

@app.get("/")
def index():
    tasks = get_db().execute(
        "SELECT id, title, done FROM tasks ORDER BY id DESC"
    ).fetchall()
    return render_template("index.html", tasks=tasks)

@app.get("/tasks/new")
def new_task():
    return render_template("new.html")

@app.post("/tasks")
def create_task():
    title = request.form.get("title", "").strip()
    body = request.form.get("body", "").strip()

    if not title or len(title) > 100 or len(body) > 1000:
        return render_template("new.html", error="Title is required and must be under 100 characters."), 400

    db = get_db()
    db.execute(
        "INSERT INTO tasks (title, body) VALUES (?, ?)",
        (title, body)
    )
    db.commit()
    return redirect(url_for("index"), code=303)

@app.get("/tasks/<int:task_id>")
def show_task(task_id):
    task = get_task(task_id)
    return render_template("show.html", task=task)

@app.get("/tasks/<int:task_id>/edit")
def edit_task(task_id):
    task = get_task(task_id)
    return render_template("edit.html", task=task)

@app.post("/tasks/<int:task_id>")
def update_task(task_id):
    get_task(task_id)
    title = request.form.get("title", "").strip()
    body = request.form.get("body", "").strip()
    done = 1 if request.form.get("done") == "on" else 0

    if not title or len(title) > 100 or len(body) > 1000:
        task = {"id": task_id, "title": title, "body": body, "done": done}
        return render_template("edit.html", task=task, error="Title is required and must be under 100 characters."), 400

    db = get_db()
    db.execute(
        "UPDATE tasks SET title = ?, body = ?, done = ? WHERE id = ?",
        (title, body, done, task_id)
    )
    db.commit()
    return redirect(url_for("show_task", task_id=task_id), code=303)

@app.post("/tasks/<int:task_id>/delete")
def delete_task(task_id):
    get_task(task_id)
    db = get_db()
    db.execute("DELETE FROM tasks WHERE id = ?", (task_id,))
    db.commit()
    return redirect(url_for("index"), code=303)

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=8000)
```

## Stop and understand only this

```text
DB_PATH controls where the database lives.
GET / lists tasks.
POST /tasks creates a task.
GET /tasks/<id> shows one task.
POST /tasks/<id> updates one task.
POST /tasks/<id>/delete deletes one task.
After POST, the app redirects.
```

Do not chase every Flask detail yet.

---

# Part 3 — Create templates

Create `templates/base.html`:

```bash
vi templates/base.html
```

```html
<!doctype html>
<html>
<head>
  <title>site6 CRUD</title>
</head>
<body>
  <header>
    <h1><a href="{{ url_for('index') }}">site6 CRUD</a></h1>
    <nav>
      <a href="{{ url_for('new_task') }}">New task</a>
    </nav>
  </header>

  <main>
    {% block content %}{% endblock %}
  </main>
</body>
</html>
```

Create `templates/index.html`:

```bash
vi templates/index.html
```

```html
{% extends "base.html" %}

{% block content %}
<h2>Tasks</h2>

<ul>
  {% for task in tasks %}
    <li>
      <a href="{{ url_for('show_task', task_id=task['id']) }}">
        {{ task["title"] }}
      </a>
      {% if task["done"] %}(done){% endif %}
    </li>
  {% else %}
    <li>No tasks yet.</li>
  {% endfor %}
</ul>
{% endblock %}
```

Create `templates/new.html`:

```bash
vi templates/new.html
```

```html
{% extends "base.html" %}

{% block content %}
<h2>New task</h2>

{% if error %}
<p><strong>{{ error }}</strong></p>
{% endif %}

<form method="POST" action="{{ url_for('create_task') }}">
  <p>
    <label>Title<br>
      <input name="title" maxlength="100">
    </label>
  </p>
  <p>
    <label>Body<br>
      <textarea name="body" maxlength="1000"></textarea>
    </label>
  </p>
  <button type="submit">Create</button>
</form>
{% endblock %}
```

Create `templates/show.html`:

```bash
vi templates/show.html
```

```html
{% extends "base.html" %}

{% block content %}
<h2>{{ task["title"] }}</h2>

<p>{{ task["body"] }}</p>

<p>Status: {% if task["done"] %}done{% else %}not done{% endif %}</p>

<p>
  <a href="{{ url_for('edit_task', task_id=task['id']) }}">Edit</a>
</p>

<form method="POST" action="{{ url_for('delete_task', task_id=task['id']) }}">
  <button type="submit">Delete</button>
</form>
{% endblock %}
```

Create `templates/edit.html`:

```bash
vi templates/edit.html
```

```html
{% extends "base.html" %}

{% block content %}
<h2>Edit task</h2>

{% if error %}
<p><strong>{{ error }}</strong></p>
{% endif %}

<form method="POST" action="{{ url_for('update_task', task_id=task['id']) }}">
  <p>
    <label>Title<br>
      <input name="title" maxlength="100" value="{{ task['title'] }}">
    </label>
  </p>
  <p>
    <label>Body<br>
      <textarea name="body" maxlength="1000">{{ task["body"] }}</textarea>
    </label>
  </p>
  <p>
    <label>
      <input type="checkbox" name="done" {% if task["done"] %}checked{% endif %}>
      Done
    </label>
  </p>
  <button type="submit">Save</button>
</form>
{% endblock %}
```

---

# Part 4 — Run locally

```bash
. .venv/bin/activate
python app.py
```

In another terminal:

```bash
curl -v http://127.0.0.1:8000/
```

Write:

```text
HTTP status:

Heading or page evidence:

What this proves:

What this does not prove:
```

---

# Part 5 — Test create with curl

```bash
curl -v -X POST   -d 'title=first task'   -d 'body=created from curl'   http://127.0.0.1:8000/tasks
```

Look for:

```text
303 See Other
Location: /
```

Then:

```bash
curl -s http://127.0.0.1:8000/ | grep 'first task'
```

Write:

```text
POST status:

Redirect location:

GET evidence after POST:

What this proves:
```

---

# Part 6 — Test in browser

Open:

```text
http://127.0.0.1:8000/
```

Create a task.

Edit it.

Delete it.

For each action, write:

```text
Action:

Browser URL before:

Browser URL after:

What changed in page:

Was there a redirect after POST?
```

---

# Part 7 — Prove persistence

Find database:

```bash
find ./data -type f -print -ls
```

Stop app with Ctrl-C.

Start again:

```bash
python app.py
```

Check old task still exists:

```bash
curl -s http://127.0.0.1:8000/ | grep 'first task'
```

Write:

```text
Database file:

Did data survive restart?

What this proves:
```

---

# Part 8 — Initialize Git

If not already:

```bash
git init
git add app.py templates
git commit -m "Create Flask Jinja CRUD app"
```

Write:

```text
Commit:

What Git protects:

What Git does not protect:
```

Expected:

```text
Git protects code history.
Git does not protect the live SQLite database unless it is intentionally backed up.
```

---

# Final reflection

```text
Task:

Framework:

Template engine:

Database:

Local database path:

Routes that use GET:

Routes that use POST:

Command that proved create:

Command that proved persistence:

What Flask helped with:

What Jinja helped with:

What infrastructure questions remain:
```

Completion checkpoint:

```text
[ ] I can run the app locally.
[ ] I can create a task.
[ ] I can view a task.
[ ] I can edit a task.
[ ] I can delete a task.
[ ] I can explain redirect-after-POST.
[ ] I can prove data survives restart.
```
