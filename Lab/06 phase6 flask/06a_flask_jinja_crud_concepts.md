# 06a — Flask + Jinja CRUD Concepts

## Purpose

Before building the app, understand the concepts.

The goal is not to memorize Flask.

The goal is to understand the web-app shape:

```text
HTTP request
→ route
→ Python function
→ database
→ template
→ HTTP response
```

---

# Part 1 — Route

A route maps a URL path to server-side code.

Example:

```python
@app.get("/")
def index():
    ...
```

Meaning:

```text
When the browser sends GET /,
run the index function.
```

## Stop and answer

```text
What URL path does this route handle?

What HTTP method does it handle?

What function runs?
```

---

# Part 2 — Template

A Jinja template is an HTML file with placeholders.

Example:

```html
<h1>{{ task["title"] }}</h1>
```

Meaning:

```text
The server has data.
Jinja inserts the data into HTML.
The browser receives finished HTML.
```

The browser does not receive Python code.

The browser does not run Jinja.

The server renders the template before sending the response.

## Stop and explain

```text
Where does Jinja run: server or browser?

What does the browser receive?

Why is this simpler than JavaScript fetch for now?
```

Expected:

```text
Jinja runs on the server.
The browser receives HTML.
This avoids adding frontend API complexity yet.
```

---

# Part 3 — CRUD

CRUD means:

```text
Create: add a task
Read: list or view tasks
Update: edit a task
Delete: remove a task
```

Possible routes:

```text
GET  /                 list tasks
GET  /tasks/new        show create form
POST /tasks            create task
GET  /tasks/<id>       show one task
GET  /tasks/<id>/edit  show edit form
POST /tasks/<id>       update task
POST /tasks/<id>/delete delete task
```

## Stop and answer

```text
Which routes only read data?

Which routes change data?

Which routes should use GET?

Which routes should use POST?
```

Expected:

```text
GET routes read/show pages.
POST routes create, update, or delete data.
```

---

# Part 4 — Redirect after POST

After a successful POST:

```text
POST /tasks
→ create task
→ redirect to GET /
```

After update:

```text
POST /tasks/3
→ update task
→ redirect to GET /tasks/3
```

After delete:

```text
POST /tasks/3/delete
→ delete task
→ redirect to GET /
```

Why?

```text
If the browser refreshes after POST, it may submit again.
Redirect moves the browser back to a safe GET page.
```

## Stop and explain

```text
What problem does redirect-after-POST solve?

What status code might be used for redirect-after-POST?

After creating a task, where should the browser go?
```

Expected:

```text
It prevents accidental duplicate actions on refresh.
303 See Other is a good redirect status after POST.
The browser should go to a GET page.
```

---

# Part 5 — Infrastructure view of CRUD

A developer may ask:

```text
How do I build this feature?
```

An infrastructure person also asks:

```text
Where is the data?
Who can write it?
How is it backed up?
How do I restore it?
How do I know the app is running?
How do I know Nginx reached it?
How do I find the error?
Can I deploy code without deleting data?
```

Both are important.

---

# Part 6 — SQLite in this phase

SQLite database:

```text
local development: ./data/site6.db
server: /var/lib/site6-app/site6.db
```

Important distinction:

```text
app.py and templates are code
site6.db is data
```

Code can be replaced from Git.

Data must be backed up.

---

# Part 7 — Minimal security habits

Do only basic habits now:

```text
validate required fields
limit title/body length
escape output through Jinja autoescaping
do not run app as root
do not make database world-writable
```

Do not add authentication yet.

---

# Final reflection

```text
What is a route?

What is a template?

Where does Jinja run?

What are the four CRUD operations?

Which operations should use POST?

Why redirect after POST?

Why is database data different from app code?

What questions would an infrastructure person ask about this app?
```

Completion checkpoint:

```text
[ ] I can explain route → function.
[ ] I can explain template → HTML.
[ ] I can explain CRUD.
[ ] I can explain GET vs POST for CRUD.
[ ] I can explain redirect-after-POST.
[ ] I can explain code vs data.
```
