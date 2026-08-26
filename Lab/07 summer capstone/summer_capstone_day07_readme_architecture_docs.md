# Day 7 — README and Architecture Documentation

---
# Part 1 — Explain request flow

Write in your own words:

```text
When a browser opens http://site6.local/, name resolution maps site6.local to app01.
The browser connects to port 80.
Nginx receives the request.
Nginx uses server_name site6.local.
Nginx proxies to 127.0.0.1:8000.
The Flask app handles the route.
Jinja renders HTML.
SQLite stores task data.
```

# Part 2 — Explain what is intentionally delayed

Add:

```markdown
## What I am not using yet
```

Include:

```text
Docker
Kubernetes
Terraform
Ansible
GitHub Actions deployment
React/Vue frontend
FastAPI
authentication
cloud deployment
```

Explain why these were delayed.

# Part 3 — Self-check

Ask:

```text
Could another beginner understand what this project does?
Could I rebuild the app from this documentation?
Could I troubleshoot a 502 from this documentation?
Could I find the database from this documentation?
Could I safely deploy from this documentation?
```