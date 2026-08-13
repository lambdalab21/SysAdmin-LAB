# 06f — Break/Fix Lab: Flask CRUD Infrastructure
# Exercise 1 — App service stopped

Break:

```bash
sudo systemctl stop site6-app
```

Test:

```bash
curl -v -H 'Host: site6.local' http://app01/
```

Check:

```bash
sudo systemctl status site6-app
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Fix:

```bash
sudo systemctl start site6-app
```

---

# Exercise 2 — Wrong proxy port

Break:

```bash
sudo vi /etc/nginx/conf.d/site6-app.conf
```

Change temporarily:

```nginx
proxy_pass http://127.0.0.1:8000;
```

to:

```nginx
proxy_pass http://127.0.0.1:8999;
```

Reload and test:

```bash
sudo nginx -t
sudo systemctl reload nginx
curl -v -H 'Host: site6.local' http://app01/
```

Check:

```bash
sudo ss -ltnp | grep 8000
sudo ss -ltnp | grep 8999 || echo "nothing listening on 8999"
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Fix back to 8000.

---

# Exercise 3 — Template error

Break a template intentionally.

For example, edit:

```bash
sudo -u deploy vi /opt/site6-app/templates/index.html
```

Remove a Jinja closing marker.

Restart app if needed:

```bash
sudo systemctl restart site6-app
```

Test:

```bash
curl -v -H 'Host: site6.local' http://app01/
```

Check:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
sudo tail -n 20 /var/log/nginx/site6.error.log
```

Fix the template, restart, and verify.

---

# Exercise 4 — Database permission failure

Back up first:

```bash
sudo systemctl stop site6-app
sudo -u site6 sh -c '
mkdir -p /var/backups/site6-app
timestamp=$(date +%Y%m%d-%H%M%S)
cp /var/lib/site6-app/site6.db /var/backups/site6-app/site6-before-permission-test-$timestamp.db
ls -lh /var/backups/site6-app/site6-before-permission-test-$timestamp.db
'
sudo systemctl start site6-app
```

Break:

```bash
sudo chown root:root /var/lib/site6-app
```

Restart:

```bash
sudo systemctl restart site6-app
```

Test create:

```bash
curl -v -H 'Host: site6.local'   -X POST   -d 'title=permission failure task'   -d 'body=should fail'   http://app01/tasks
```

Check:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
ls -ld /var/lib/site6-app
ls -l /var/lib/site6-app/site6.db
```

Fix:

```bash
sudo chown -R site6:site6 /var/lib/site6-app
sudo systemctl restart site6-app
```

---

# Exercise 5 — Wrong database path

Break:

```bash
sudo vi /etc/systemd/system/site6-app.service
```

Change:

```ini
Environment=SITE6_DB=/var/lib/site6-app/site6.db
```

to:

```ini
Environment=SITE6_DB=/var/lib/site6-app/wrong/site6.db
```

Reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart site6-app
sudo systemctl status site6-app
```

Check:

```bash
sudo journalctl -u site6-app --since "10 minutes ago"
```

Fix back, reload, restart, and verify old tasks return.