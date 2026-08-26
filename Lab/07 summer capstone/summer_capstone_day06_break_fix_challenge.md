# Day 6 — Break/Fix Challenge Without Notes

# Failure 1 — Backend service stopped

Break:

```bash
sudo systemctl stop site6-app
```

Test:

```bash
curl -v -H 'Host: site6.local' http://app01/
```

# Failure 2 — Wrong Nginx proxy port

Change proxy target to port 8999, reload Nginx, troubleshoot, then fix back to 8000.
# Failure 3 — Bad database ownership

Break:

```bash
sudo chown root:root /var/lib/site6-app
sudo systemctl restart site6-app
```

Try creating a task. Troubleshoot. Fix:

```bash
sudo chown -R site6:site6 /var/lib/site6-app
sudo systemctl restart site6-app
```

# Failure 4 — Wrong Host header

Run:

```bash
curl -v -H 'Host: wrong.local' http://app01/
curl -v -H 'Host: site6.local' http://app01/
```
# Failure 5 — Browser name resolution problem

Use a fake name or temporarily remove `site6.local` from browser machine `/etc/hosts`.

Run:

```bash
getent hosts site6.local
curl -v http://site6.local/
curl -v -H 'Host: site6.local' http://app01/
```

# Deliverable

Create or improve:

```text
TROUBLESHOOTING.md
```

Required sections:

```text
# Troubleshooting

## General method
## 502 Bad Gateway
## App service stopped
## Wrong proxy port
## Database permission problem
## Name resolution problem
## Failed POST
## Commands by layer
```