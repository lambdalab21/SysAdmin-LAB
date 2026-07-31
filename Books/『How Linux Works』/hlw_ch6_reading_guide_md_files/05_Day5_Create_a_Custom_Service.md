# Day 5 — Create a Custom systemd Service

## Goal

Create a simple service so systemd stops being abstract.

This lab creates a tiny HTTP server managed by systemd.

## Step 1: Create content

```bash
sudo mkdir -p /srv/hlw-ch6-web
echo '<h1>HLW Ch. 6 systemd service</h1>' | sudo tee /srv/hlw-ch6-web/index.html
```

## Step 2: Create a script

```bash
sudo tee /usr/local/bin/hlw-ch6-web.sh > /dev/null <<'SH'
#!/usr/bin/env bash
set -euo pipefail

exec python3 -m http.server 8088 --directory /srv/hlw-ch6-web
SH

sudo chmod 755 /usr/local/bin/hlw-ch6-web.sh
```

## Step 3: Create unit file

```bash
sudo tee /etc/systemd/system/hlw-ch6-web.service > /dev/null <<'UNIT'
[Unit]
Description=How Linux Works Ch. 6 practice web service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/hlw-ch6-web.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
UNIT
```

## Step 4: Load and start

```bash
sudo systemctl daemon-reload
sudo systemctl start hlw-ch6-web
systemctl status hlw-ch6-web
```

Test:

```bash
curl http://localhost:8088
```

## Step 5: Enable at boot

```bash
sudo systemctl enable hlw-ch6-web
systemctl is-enabled hlw-ch6-web
```

## Step 6: Inspect

```bash
systemctl cat hlw-ch6-web
systemctl show hlw-ch6-web --property=MainPID,ExecStart,Restart,ControlGroup
journalctl -u hlw-ch6-web --since "10 minutes ago"
ss -tulpn | grep ':8088'
```

## Questions

1. Why place the unit in `/etc/systemd/system`?
2. What does `ExecStart` do?
3. What does `Restart=on-failure` do?
4. Why run `daemon-reload`?
5. What does `WantedBy=multi-user.target` mean?
6. Which process owns port `8088`?
7. Where do logs appear?

## Cleanup option

If removing:

```bash
sudo systemctl disable --now hlw-ch6-web
sudo rm /etc/systemd/system/hlw-ch6-web.service
sudo rm /usr/local/bin/hlw-ch6-web.sh
sudo rm -rf /srv/hlw-ch6-web
sudo systemctl daemon-reload
```

## Exit criterion

He can say:

```text
A service unit describes how to start and manage a process. After creating or editing a unit file, use daemon-reload.
```
