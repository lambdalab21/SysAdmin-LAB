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

## 5. Deployment

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
