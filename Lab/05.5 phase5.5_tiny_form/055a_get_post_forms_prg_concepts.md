# 055a — GET, POST, Forms, and Redirect-after-POST

## Purpose

Before building the app, understand the web flow.

The goal:

```text
browser asks for page
→ server returns HTML form
→ browser submits form
→ server receives data
→ server saves data
→ server redirects
→ browser asks for page again
```

---

# Part 1 — GET

GET means:

```text
Please give me this resource.
```

Examples:

```text
GET /
GET /about
GET /messages
```

When you open a URL in a browser, the browser usually sends GET.

Example:

```text
http://site55.local/
```

means the browser sends something like:

```http
GET / HTTP/1.1
Host: site55.local
```

## Stop and answer

```text
When I type a URL in a browser, what HTTP method is usually used? Typing a URL in a browser usually sends GET. 

Should GET change important server data? No. GET should not change important server data. 

What should GET / do in the message-board app? GET / should show the main page with the form and list of messages. 
```

---

# Part 2 — POST

POST means:

```text
Here is data for the server to process.
```

Example:

```text
POST /messages
```

The browser sends this when a form has:

```html
<form method="POST" action="/messages">
```

## Stop and answer

```text
What method sends form data? POST sends form data. 

What path receives the message? POST /messages recieves the message. 

Should POST /messages show all messages directly, or redirect? After saving it, it should redirect to GET /.
```

---

# Part 3 — HTML form

A minimal form:

```html
<form method="POST" action="/messages">
  <input name="body">
  <button type="submit">Add message</button>
</form>
```

Important parts:

```text
method="POST"          use POST
action="/messages"     send data to /messages
name="body"            field name is body
```

The submitted data looks roughly like:

```text
body=hello
```

## Stop and explain

```text
What does method="POST" mean? means send data to the server using the POST HTTP method. 

What does action="/messages" mean? Submit the form data to the /messages path. 

Why does the input need name="body"? The input neds name="body" so the server can identify and read the field's value. 

What data should the server read? The server should read the body field data. 
```

---

# Part 4 — Redirect after POST

Bad pattern:

```text
POST /messages
→ save message
→ directly return full page
```

Better pattern:

```text
POST /messages
→ save message
→ redirect to /
→ browser sends GET /
→ server shows page
```

This is often called:

```text
POST / Redirect / GET
```

You do not need to memorize the acronym. Understand the behavior.

## Why redirect?

If the browser stays on the POST response, refreshing may submit the form again.

Redirect moves the browser back to a safe GET page.

## Stop and answer

```text
What problem can happen if refresh repeats POST? Refresh can re-submit the POST and duplicate the message. 

What should the server send after saving a message? The server should send a redirect to /. 

What should the browser do after receiving the redirect? The browser should follow the redirect by sending GET /. 
```

---

# Part 5 — Server-rendered HTML

In this phase, the server returns finished HTML.

The browser does not call a JSON API.

The browser does not need JavaScript.

```text
SQLite rows
→ server code
→ HTML string
→ browser displays page
```

This is simpler than:

```text
browser JavaScript
→ fetch API
→ JSON
→ update DOM
```

That comes later.

---

# Final reflection

```text
What does GET mean? GET means "retrieve this resource". 

What does POST mean? POST means "here is data to process". 

What does an HTML form do? An HTML form collects user input and sends it to the server. 

What does action="/messages" mean? action="/messages" tells the form where to send the data. 

What does name="body" mean? name="body" identifies the input field so that the server can access its value. 

Why redirect after POST? Redirect after POST prevents duplicate submissions on fersh. 

Why avoid JavaScript for now? Avoid JavaScript for now to keep it simple with server-rendered HTML. 
```
