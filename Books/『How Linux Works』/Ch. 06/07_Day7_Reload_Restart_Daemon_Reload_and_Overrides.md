# Day 7 — Reload, Restart, daemon-reload, and Overrides

## Goal

Understand three commonly confused actions:

```text
restart       = stop and start the service process
reload        = ask service to reread its own config, if supported
daemon-reload = ask systemd to reread unit files
```

## Lab 1: Does the service support reload?

```bash
systemctl show nginx --property=ExecReload
systemctl show hlw-ch6-web --property=ExecReload
```

Questions:

1. Does Nginx define `ExecReload`?
2. Does the custom Python service define `ExecReload`?
3. Why might `systemctl reload` fail or do nothing useful for some services?

## Lab 2: Modify a unit file and use daemon-reload

Change description:

```bash
sudo sed -i 's/How Linux Works Ch. 6 practice web service/HLW Ch. 6 changed description/' /etc/systemd/system/hlw-ch6-web.service
```

Check status:

```bash
systemctl status hlw-ch6-web
```

Now reload unit files:

```bash
sudo systemctl daemon-reload
systemctl status hlw-ch6-web
```

Questions:

1. Why did systemd need `daemon-reload`?
2. Did `daemon-reload` restart the service?
3. Why is `daemon-reload` not the same as `restart`?

## Lab 3: Use an override

```bash
sudo systemctl edit hlw-ch6-web
```

Add:

```ini
[Service]
Restart=always
```

Inspect:

```bash
systemctl cat hlw-ch6-web
systemctl show hlw-ch6-web --property=Restart
```

Questions:

1. Where was the override file created?
2. Why use overrides instead of editing vendor unit files?
3. How does `systemctl cat` display combined configuration?

Remove override:

```bash
sudo systemctl revert hlw-ch6-web
sudo systemctl daemon-reload
systemctl show hlw-ch6-web --property=Restart
```

## Lab 4: Nginx reload versus restart

```bash
sudo nginx -t
sudo systemctl reload nginx
systemctl status nginx

sudo systemctl restart nginx
systemctl status nginx
```

Questions:

1. Why test Nginx config before reload?
2. Why might reload be less disruptive?
3. When is restart necessary?

## Exit criterion

He can say:

```text
If I change a unit file, I use daemon-reload. If I want the process to start over, I use restart. If the service supports rereading config, I use reload.
```
