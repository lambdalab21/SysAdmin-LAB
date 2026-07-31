# Day 6 — Debug Failed Services

## Goal

Debug failed services with evidence.

Correct sequence:

```text
status
→ logs
→ unit file
→ executable path
→ permissions
→ process/port
→ fix one thing
→ verify
```

Use the custom service from Day 5.

Verify it works:

```bash
systemctl status hlw-ch6-web
curl http://localhost:8088
```

## Failure 1: Bad executable path

Break:

```bash
sudo cp /etc/systemd/system/hlw-ch6-web.service /etc/systemd/system/hlw-ch6-web.service.bak
sudo sed -i 's|/usr/local/bin/hlw-ch6-web.sh|/usr/local/bin/missing-script.sh|' /etc/systemd/system/hlw-ch6-web.service
sudo systemctl daemon-reload
sudo systemctl restart hlw-ch6-web
```

Diagnose:

```bash
systemctl status hlw-ch6-web
journalctl -u hlw-ch6-web --since "5 minutes ago"
systemctl cat hlw-ch6-web
```

Fix:

```bash
sudo mv /etc/systemd/system/hlw-ch6-web.service.bak /etc/systemd/system/hlw-ch6-web.service
sudo systemctl daemon-reload
sudo systemctl restart hlw-ch6-web
```

## Failure 2: Script lacks execute permission

Break:

```bash
sudo chmod 644 /usr/local/bin/hlw-ch6-web.sh
sudo systemctl restart hlw-ch6-web
```

Diagnose:

```bash
systemctl status hlw-ch6-web
journalctl -u hlw-ch6-web --since "5 minutes ago"
ls -l /usr/local/bin/hlw-ch6-web.sh
```

Fix:

```bash
sudo chmod 755 /usr/local/bin/hlw-ch6-web.sh
sudo systemctl restart hlw-ch6-web
```

## Failure 3: Port already in use

```bash
sudo systemctl stop hlw-ch6-web
python3 -m http.server 8088 --directory /tmp &
CONFLICT_PID=$!
sudo systemctl start hlw-ch6-web
```

Diagnose:

```bash
systemctl status hlw-ch6-web
journalctl -u hlw-ch6-web --since "5 minutes ago"
ss -tulpn | grep ':8088'
lsof -i :8088
```

Fix:

```bash
kill "$CONFLICT_PID"
sudo systemctl restart hlw-ch6-web
```

## Questions

1. Which error appeared for the missing script?
2. Which error appeared for bad permissions?
3. Which tool identified the port conflict?
4. Why is `journalctl` better than guessing?
5. Why make only one controlled break at a time?

## Exit criterion

He can diagnose:

```text
bad ExecStart path
bad executable permission
port conflict
```

without being told the answer.
