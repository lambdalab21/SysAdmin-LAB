
## Name resolution

### `getent hosts site6.local`
```text
Command: getent hosts site6.local
What it does: Looks up site6.local using the system's configured name resolution (NSS — checks /etc/hosts, then DNS, per /etc/nsswitch.conf).
What output I expect: An IP address (e.g. app01's IP) followed by "site6.local".
What it proves: This machine can resolve the hostname to an IP address.
What it does not prove: That the IP is reachable, that anything is listening on port 80, or that Nginx will serve the right site block.
```

---

## HTTP client

### `curl -v http://app01/`
```text
Command: curl -v http://app01/
What it does: Connects directly to app01 over HTTP and sends a request with Host: app01 (curl fills in the Host header from the URL).
What output I expect: A response — likely Nginx's default site or a different server block than site6, since server_name won't match "app01".
What it proves: The machine app01 is reachable on port 80 and something is listening.
What it does not prove: That the site6 server block was chosen, or that the app behind it works.
```

### `curl -v -H 'Host: site6.local' http://app01/`
```text
Command: curl -v -H 'Host: site6.local' http://app01/
What it does: Connects to app01 by IP/hostname but manually overrides the Host header to site6.local, simulating what a browser sends after DNS resolution.
What output I expect: The site6 app's HTML response, HTTP 200.
What it proves: Nginx correctly matches server_name site6.local and proxies to the backend, independent of whether DNS/hosts resolution for site6.local works.
What it does not prove: That a real browser using http://site6.local/ will resolve the name correctly — that's a separate, earlier step.
```

### `curl -v http://site6.local/`
```text
Command: curl -v http://site6.local/
What it does: Resolves site6.local using normal name resolution, connects, and sends Host: site6.local automatically — this is the closest command to real browser behavior.
What output I expect: The site6 app's HTML response, HTTP 200, same as the manual Host-header version.
What it proves: End-to-end path works — name resolution, network reachability, Nginx routing, and the backend app all functioning together.
What it does not prove: Nothing further upstream — this is the most complete single check, so a failure here means I need to narrow down which earlier layer broke.
```

### `curl -v -H 'Host: wrong.local' http://app01/`
```text
Command: curl -v -H 'Host: wrong.local' http://app01/
What it does: Sends a Host header that does not match any server_name in the Nginx config.
What output I expect: Nginx's default server block response (often a 404 or a different site's content), not the site6 app.
What it proves: Nginx uses the Host header — not the destination IP — to decide which site to serve, and confirms which server block is the default.
What it does not prove: Which specific server_name values are configured (need to check the config directly for that).
```

### `curl -s -H 'Host: site6.local' http://app01/ | grep 'backup proof task'`
```text
Command: curl -s -H 'Host: site6.local' http://app01/ | grep 'backup proof task'
What it does: Fetches the rendered page silently (-s suppresses progress output) and searches the HTML for a known piece of task data.
What output I expect: A matching line containing "backup proof task" if the data is present.
What it proves: A specific database row is actually being rendered by the app right now — end-to-end evidence, not just "the page loads."
What it does not prove: Anything about other rows, or that the data will survive a future deployment.
```

---

## Nginx

### `sudo nginx -t`
```text
Command: sudo nginx -t
What it does: Parses and validates the Nginx configuration syntax without reloading or restarting the service.
What output I expect: "syntax is ok" and "test is successful" on two lines.
What it proves: The config files are syntactically valid and safe to reload.
What it does not prove: That the config does what I intend (e.g. proxy_pass pointing at the right port) — only that it won't crash Nginx.
```

### `sudo grep -R "server_name\|proxy_pass\|access_log\|error_log" /etc/nginx/conf.d/site6-app.conf`
```text
Command: sudo grep -R "server_name\|proxy_pass\|access_log\|error_log" /etc/nginx/conf.d/site6-app.conf
What it does: Searches the site6 Nginx config file for the four directives that define routing and logging.
What output I expect: Lines showing server_name site6.local;, proxy_pass http://127.0.0.1:8000;, and paths to the access/error logs.
What it proves: What Nginx is *configured* to do — which hostname it should match and where it should forward traffic.
What it does not prove: That Nginx is actually behaving this way at runtime (need curl/log evidence for that) or that the backend on that port exists.
```

### `sudo tail -n 20 /var/log/nginx/site6.access.log`
```text
Command: sudo tail -n 20 /var/log/nginx/site6.access.log
What it does: Shows the most recent 20 lines of the site6-specific Nginx access log.
What output I expect: One line per request, with client IP, timestamp, request line, HTTP status, and bytes sent.
What it proves: That Nginx actually received and logged a specific request, and what status code it returned.
What it does not prove: What the backend app did internally to produce that response — that's in the app's own logs.
```

### `sudo tail -n 20 /var/log/nginx/site6.error.log`
```text
Command: sudo tail -n 20 /var/log/nginx/site6.error.log
What it does: Shows the most recent 20 lines of the site6-specific Nginx error log.
What output I expect: Normally empty/quiet; during a failure, entries like "connect() failed (111: Connection refused) while connecting to upstream".
What it proves: Whether Nginx itself is failing to reach the backend (proxy-layer failure), and gives the exact upstream address it tried.
What it does not prove: Why the backend is unreachable (stopped service, wrong port, permissions) — that requires systemd/journal evidence.
```

---

## systemd

### `sudo systemctl status site6-app`
```text
Command: sudo systemctl status site6-app
What it does: Shows the current state of the site6-app systemd unit — active/inactive, main PID, recent log lines, and enabled state.
What output I expect: "Active: active (running)" with a Main PID and a short tail of recent journal lines.
What it proves: Whether the process is currently running under systemd's supervision right now.
What it does not prove: That the app is listening on the correct port, or that Nginx can reach it.
```

### `systemctl is-enabled site6-app`
```text
Command: systemctl is-enabled site6-app
What it does: Reports whether the unit is set to start automatically on boot.
What output I expect: "enabled" or "disabled".
What it proves: Boot-time behavior only — start vs enable are different concepts (start = running now, enable = will run after reboot).
What it does not prove: Whether the service is currently running.
```

### `sudo systemctl stop site6-app` / `start` / `restart`
```text
Command: sudo systemctl stop|start|restart site6-app
What it does: Stops, starts, or (stop+start in sequence) restarts the managed process.
What output I expect: No output on success; systemctl status afterward confirms the new state.
What it proves: I can safely control the service lifecycle for maintenance, backups, and deployments.
What it does not prove: That the app will come back up cleanly — always verify with status + a curl test afterward.
```

### `sudo journalctl -u site6-app --since "10 minutes ago"`
```text
Command: sudo journalctl -u site6-app --since "10 minutes ago"
What it does: Shows systemd journal entries (stdout/stderr + lifecycle events) for the site6-app unit within the given time window.
What output I expect: Startup lines, request-handling output (if the app logs requests), and any tracebacks/errors.
What it proves: What the application itself printed or logged, and confirms the app-layer's view of a request or failure.
What it does not prove: Whether the request actually reached the app through Nginx (correlate with the Nginx access/error logs).
```

---

## Process and port

### `sudo ss -ltnp | grep 8000`
```text
Command: sudo ss -ltnp | grep 8000
What it does: Lists listening TCP sockets (-l listening, -t TCP, -n numeric, -p show owning process) and filters for port 8000.
What output I expect: A line like LISTEN 0 128 127.0.0.1:8000 0.0.0.0:* users:(("python",pid=NNNN,...)).
What it proves: A specific process is bound to port 8000 and on which interface (127.0.0.1 = local-only, 0.0.0.0 = all interfaces).
What it does not prove: That the process responds correctly to HTTP requests — only that something is listening.
```

### `curl -v http://127.0.0.1:8000/`
```text
Command: curl -v http://127.0.0.1:8000/
What it does: Talks directly to the Flask app's port, bypassing Nginx entirely.
What output I expect: HTTP 200 and the app's HTML, if the backend is healthy.
What it proves: The backend app itself works correctly, isolated from any Nginx/proxy issue.
What it does not prove: That Nginx can reach or proxy to it (a firewall or wrong proxy_pass target could still break that).
```

---

## Files and permissions

### `ls -ld /var/lib/site6-app`
```text
Command: ls -ld /var/lib/site6-app
What it does: Shows ownership, permissions, and metadata for the data directory itself (-d shows the directory, not its contents).
What output I expect: drwxr-x--- ... site6 site6 ... /var/lib/site6-app (or similar), owned by the site6 service account.
What it proves: Who owns the directory the app needs to write into, which determines whether the app's process user can create/modify files there.
What it does not prove: Whether the database file inside has matching ownership — check it separately.
```

### `ls -lh /var/lib/site6-app/site6.db`
```text
Command: ls -lh /var/lib/site6-app/site6.db
What it does: Shows ownership, permissions, and human-readable (-h) size of the SQLite database file.
What output I expect: -rw-r----- ... site6 site6 ... site6.db with a nonzero size.
What it proves: The database file exists, roughly how much data it holds, and who owns it.
What it does not prove: Whether the data inside is valid or current — size alone doesn't confirm content.
```

### `id site6`
```text
Command: id site6
What it does: Shows the UID, GID, and group memberships of the site6 service account.
What output I expect: uid=NNN(site6) gid=NNN(site6) groups=NNN(site6).
What it proves: That the site6 user/group exists and its numeric IDs, useful for cross-checking against file ownership.
What it does not prove: That the systemd unit actually runs as this user (check the unit file's User= directive for that).
```

### `sudo -u site6 test -r /var/lib/site6-app/site6.db && echo site6-can-read`
```text
Command: sudo -u site6 test -r /var/lib/site6-app/site6.db && echo site6-can-read
What it does: Runs a read-permission check as the site6 user specifically, rather than inferring permission from ls output.
What output I expect: "site6-can-read" printed if the permission check passes.
What it proves: Direct, unambiguous evidence that this exact user can read the file right now.
What it does not prove: Write access — that's a separate test (test -w).
```

### `sudo chown -R site6:site6 /var/lib/site6-app`
```text
Command: sudo chown -R site6:site6 /var/lib/site6-app
What it does: Recursively (-R) sets the owning user and group of the directory and everything inside it to site6.
What output I expect: No output on success.
What it proves: Nothing by itself — it's a fix, not evidence. Follow with ls -ld / ls -lh and a functional test (POST + restart) to prove the fix worked.
What it does not prove: That the app will restart cleanly — verify with systemctl status and a curl test.
```

---

## Database and backup

### SQLite backup copy
```text
Command: sudo -u site6 sh -c 'cp /var/lib/site6-app/site6.db /var/backups/site6-app/site6-<timestamp>.db'
What it does: Copies the SQLite database file to a timestamped backup location, run as the site6 user so ownership on the copy is correct.
What output I expect: No output from cp; a follow-up ls -lh shows the new backup file with a size similar to the live database.
What it proves: A point-in-time copy of the data exists outside the live path.
What it does not prove: That the copy is internally consistent — for SQLite, the service should be stopped first (or a proper backup API used) to avoid copying a file mid-write.
```

### `sudo -u site6 sqlite3 /tmp/site6-restore-test/site6.db 'SELECT id, title, done FROM tasks;'`
```text
Command: sudo -u site6 sqlite3 /tmp/site6-restore-test/site6.db 'SELECT id, title, done FROM tasks;'
What it does: Opens the restored copy of the database directly with the sqlite3 CLI and queries the tasks table.
What output I expect: Pipe-delimited rows of id|title|done for every task in the backup.
What it proves: The backup file is a valid, readable SQLite database containing real task rows — not just a same-sized file.
What it does not prove: That this data matches the *current* live database — only that the backup itself is intact.
```

---

## Git and deployment

### `git status`
```text
Command: git status
What it does: Shows the current branch and whether the working tree has uncommitted changes.
What output I expect: "On branch main" / "nothing to commit, working tree clean" (or a list of modified files).
What it proves: Whether what's on disk locally matches the last commit — i.e., whether I'm about to deploy exactly what I think I am.
What it does not prove: That the remote server (app01) is running this same code — that requires deploying and verifying.
```

### `git diff`
```text
Command: git diff
What it does: Shows line-by-line differences between the working tree and the last commit.
What output I expect: Unified diff output (+/- lines) for any changed files, or nothing if clean.
What it proves: Exactly what changed, in detail, before I commit or deploy it.
What it does not prove: That the change is correct or safe — I still have to reason about the diff myself.
```

### `rsync -avn --delete --exclude '.git' --exclude '.venv' --exclude 'data' ./ deploy@app01:/opt/site6-app/`
```text
Command: rsync -avn --delete --exclude '.git' --exclude '.venv' --exclude 'data' ./ deploy@app01:/opt/site6-app/
What it does: Dry-run (-n) sync showing exactly what rsync *would* copy or delete on app01, without actually touching anything. -a preserves attributes, -v is verbose, --delete would remove remote files not present locally.
What output I expect: A list of files that would be transferred, ending with "(DRY RUN)" — no actual changes made.
What it proves: What a real deployment would change or delete, letting me confirm it's safe (e.g. it should never mention /var/lib/site6-app or site6.db) before running it for real.
What it does not prove: That the code will work once deployed — only what files would move.
```

---

## Logs

### Cross-referencing logs across layers
```text
Command: sudo journalctl -u site6-app --since "..." AND sudo tail -n 20 /var/log/nginx/site6.access.log AND sudo tail -n 20 /var/log/nginx/site6.error.log
What it does: Reads the three primary log sources for this stack together, in the same time window.
What output I expect: A consistent story — e.g. an Nginx access-log entry with a 502, a matching Nginx error-log line about connection refused, and either silence or a crash trace in the app journal.
What it proves: Where in the stack a failure actually occurred, by correlating timestamps across independent sources rather than guessing from one log alone.
What it does not prove: Root cause by itself — logs tell me *where*, but I still confirm the *why* with a targeted command (ss, systemctl status, ls -ld, etc.).
```