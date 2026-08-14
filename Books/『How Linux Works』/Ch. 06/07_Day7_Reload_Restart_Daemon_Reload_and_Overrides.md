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

1. Does Nginx define `ExecReload`? Yes. 
2. Does the custom Python service define `ExecReload`? No. 
3. Why might `systemctl reload` fail or do nothing useful for some services? The unit has no ExecReload. 

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

1. Why did systemd need `daemon-reload`? Systemd caches unit files; it must reread them after edits. 
2. Did `daemon-reload` restart the service? No. 
3. Why is `daemon-reload` not the same as `restart`? daemon-reload only updates systemd's view of the unit files; restart stops and starts the actual process. 

## Lab 3: Use an override

```bash
sudo systemctl edit hlw-ch6-web
```

Questions:

1. Where was the override file created? /etc/systemd/system/hlw-ch6-web.service.d/override.conf
2. Why use overrides instead of editing vendor unit files? Package updates can overwrite vendor unit files, overrides are safer. 
3. How does `systemctl cat` display combined configuration? It shows the base unit file plus any drop-in overrides merged together. 

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

1. Why test Nginx config before reload? To catch syntax errors before applying the config. 
2. Why might reload be less disruptive? It keeps the process running and usually preserves existing connections. 
3. When is restart necessary? When the service doesn't support reload, or when a full process restart is required. 
