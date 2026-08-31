## Request flow
Browser opens http://site6.local/.  
DNS/hosts resolves site6.local to the server IP.  
Request hits Nginx on port 80.  
Nginx matches server_name site6.local and location /.  
Nginx uses proxy_pass to forward the request to the backend (Gunicorn/Flask, usually localhost or Unix socket).  
systemd-managed site6-app process (Gunicorn + Flask) receives it.  
Flask routes the request, renders Jinja template if needed, and talks to SQLite via the path in SITE6_DB.  
Response goes back: SQLite → Flask → Gunicorn → Nginx → browser.

## Nginx
Nginx is the reverse proxy and web server that accepts public HTTP traffic and forwards it to the app.  
server_name tells Nginx which hostname this server block handles (site6.local).  
proxy_pass is the directive that sends the request to the upstream app (e.g. http://127.0.0.1:PORT or a Unix socket).  
Nginx listens on port 80 because that is the standard unprivileged HTTP port clients use; the app itself stays on a private port/socket.

## systemd
systemd manages services: starts them, restarts on failure, and can start them at boot.  
ExecStart is the exact command that launches the process (usually the Gunicorn/Flask binary with options).  
Environment=SITE6_DB sets the environment variable that tells the app where the SQLite file lives.  
start = run the service now; enable = make it start automatically on boot.

## Flask and Jinja
Flask is the Python web framework that defines routes and handles request/response logic.  
Jinja is the template engine Flask uses to render HTML pages with dynamic data.

## SQLite
The database is a single file (path given by SITE6_DB).  
It is owned by the user the app runs as (often www-data or a dedicated app user).  
It lives outside /opt/site6-app so code deploys/rsyncs do not overwrite or delete live data.  
Backup: copy the .db file (or use sqlite3 .backup).  
Test restore: stop the app, replace the live .db with the backup, start the app, and verify data is present.

## Deployment
Safe sequence: git pull → git diff (review changes) → rsync --dry-run → real rsync (code only) → restart service → check old data still exists → test the site.  
git diff shows exactly what will change.  
rsync dry-run shows what files would be copied/deleted without touching anything.  
Checking old data confirms the database was not overwritten.

## Backup and restore
Backup = copy the SQLite file (or use the .backup command) to a safe location.  
Restore = stop service, put the backup file in place, start service, verify data.

## Troubleshooting
502: backend not running or unreachable → check systemctl status site6-app, journalctl, Nginx error log, curl the upstream directly.  
Failed POST: app error, permission, or form validation → check app logs, Nginx error log, and that the request reaches Flask.  
Wrong site showing: server_name mismatch or wrong config enabled → check Nginx sites-enabled and server_name.  
Data missing: wrong DB path, permissions, or restore not done → verify SITE6_DB, file ownership, and that the correct .db is in use.

## What I intentionally delayed
Docker, complex orchestration, and extra tools until the basic Nginx → systemd → Flask → SQLite path was solid and explainable.
```