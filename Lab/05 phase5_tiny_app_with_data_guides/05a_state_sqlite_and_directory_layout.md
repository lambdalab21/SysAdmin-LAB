# 05a — State, SQLite, and Directory Layout

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
---
# Part 4 — Code vs data

## Stop and answer

```text
Which directory should rsync update? rsync may update app code. 
Which directory should rsync never delete? rsync must not delete the data directory. 
Where should the SQLite database live? The database should be under /var/lib/site5-app. 
Why not keep the database inside /opt/site5-app? If it lives inside the code directory, deployment may delete it. 
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
Should the app run as root? The app should not run as root. 
Who should write the database? The app service user should write the database. 
Should Nginx write the database? NGinx should not write the database. 
Should deploy user casually edit live data? The deploy user should update code, not manually edit live data. 
```