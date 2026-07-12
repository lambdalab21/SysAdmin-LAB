#LAB #deployment #backend
# 05a — State, SQLite, and Directory Layout

## Purpose

Understand what changes when an app has data.

Phase 4:

```text
code → process → port → Nginx
```

Phase 5:

```text
code → process → port → Nginx → data file
```

The new risk:

```text
deploying code must not destroy data
```

---

# Part 1 — What is state?

State is information that survives after the program stops.

Examples:

```text
notes
tasks
users
settings
database rows
uploaded files
```

## Stop and answer

```text
What data should survive if the app process stops?
What data would be bad to lose?
Is app code the same kind of thing as user data?
Why not?
```

Expected:

```text
Code can be recreated from Git.
User data may exist only on the server unless backed up.
```

---

# Part 2 — Why SQLite first?

SQLite stores a database in a normal file:

```text
/var/lib/site5-app/site5.db
```

This teaches persistence without adding a separate database server yet.

It teaches:

```text
file location
ownership
permissions
backup
restore
```

It does not yet teach:

```text
MySQL users
grants
network database access
replication
performance tuning
```

---

# Part 3 — Directory layout

Use:

```text
/opt/site5-app/              app code
/var/lib/site5-app/          persistent data
/etc/systemd/system/         service instructions
/etc/nginx/conf.d/           Nginx site config
/var/log/nginx/              Nginx logs
```

Feynman version:

```text
/opt/site5-app is the program shelf.
/var/lib/site5-app is the notebook shelf.
/etc is the instruction shelf.
/var/log is the record shelf.
```

---

# Part 4 — Code vs data

Write by hand:

```text
Thing             Example path                         Safe to overwrite?
App code          /opt/site5-app/app.py                yes, if Git has it
Database          /var/lib/site5-app/site5.db          no
Nginx config      /etc/nginx/conf.d/site5-app.conf     only intentionally
Systemd service   /etc/systemd/system/site5-app.service only intentionally
```

## Stop and answer

```text
Which directory should rsync update?
Which directory should rsync never delete?
Where should the SQLite database live?
Why not keep the database inside /opt/site5-app?
```

Expected:

```text
rsync may update app code.
rsync must not delete the data directory.
The database should live under /var/lib/site5-app.
If it lives inside the code directory, deployment may delete it.
```

---

# Part 5 — Users and ownership

Recommended model:

```text
deploy user: copies code
site5 user: runs app and owns data
nginx user: receives public HTTP requests
```

## Stop and answer

```text
Should the app run as root?
Who should write the database?
Should Nginx write the database?
Should deploy user casually edit live data?
```

Expected:

```text
The app should not run as root.
The app service user should write the database.
Nginx should not write the database.
The deploy user should update code, not manually edit live data.
```

---

# Commands to understand later

```bash
ls -ld /opt/site5-app
ls -ld /var/lib/site5-app
ls -l /var/lib/site5-app/site5.db
id site5
sudo -u site5 test -w /var/lib/site5-app && echo site5-can-write-data
```

For each command, write:

```text
What this proves:
What this does not prove:
```

---

# Final reflection

```text
What is state?
Why is data different from code?
Why use SQLite first?
Where should code live?
Where should data live?
Which user should write the database?
Why must deployment avoid deleting /var/lib/site5-app?
```
