# Phase 5.5 Overview — Tiny Form App

## Purpose

This phase sits between:

```text
Phase 5: tiny backend app with data
```

and:

```text
Phase 6: full CRUD app
```

Do not jump directly to CRUD yet.

First learn the smaller web cycle:

```text
GET page
→ show HTML form
→ submit POST
→ validate input
→ write SQLite data
→ redirect
→ show updated page
```

This is the bridge between a simple backend and a real web application.

---

# Big idea

A web app is not only:

```text
server returns text
```

A web app can also:

```text
receive user input
change persistent state
return a new page
```

In this phase, build a tiny message board:

```text
GET /              show messages and a form
POST /messages     add one message
```

That is all.

No edit.  
No delete.  
No login.  
No JavaScript.  
No REST API.  
No frontend build tools.

---

# Feynman version

Explain it to a younger student:

```text
The browser asks for a page.
The server gives back a page with a form.
The user writes a message and presses submit.
The browser sends the message to the server.
The server saves the message in its notebook.
The browser is sent back to the main page.
The main page now shows the new message.
```

---

# What this phase teaches

## 1. HTML forms

You will learn that the browser can send data without JavaScript:

```html
<form method="POST" action="/messages">
  <input name="body">
  <button type="submit">Add message</button>
</form>
```

## 2. GET vs POST

Simple rule:

```text
GET asks for a page.
POST sends data that may change something.
```

## 3. Redirect after POST

The app should not respond to POST by directly showing the page.

Better pattern:

```text
POST /messages
→ save message
→ redirect to /
→ browser sends GET /
→ server shows page
```

This prevents accidental duplicate submission when refreshing.

## 4. Server-rendered HTML

The server creates the HTML.

```text
database rows
→ Python code
→ HTML page
→ browser
```

## 5. Persistence

Messages should survive app restart because they live in SQLite.

## 6. Deployment

Same infrastructure pattern:

```text
Nginx
→ systemd service
→ app on 127.0.0.1:8000
→ SQLite database in /var/lib/site55-app/
```

---

# Files in this phase

Use these in order:

```text
055a_get_post_forms_prg_concepts.md
055b_build_tiny_message_board_locally.md
055c_deploy_message_board_systemd_nginx.md
055d_backup_restore_and_safe_deploy_message_board.md
055e_break_fix_tiny_form_app.md
```

---

# What not to learn yet

Do not add:

```text
edit messages
delete messages
user accounts
sessions
passwords
JavaScript fetch
REST API
React/Vue/Svelte
Docker
GitHub Actions deployment
```

Those are later.

---

# Completion standard

At the end, you should be able to explain:

```text
What GET does.
What POST does.
What an HTML form sends.
Why the server redirects after POST.
Where the database lives.
Why data survives restart.
How systemd runs the app.
How Nginx reaches the app.
How to prove the browser request reached Nginx.
How to prove the app wrote data.
```
