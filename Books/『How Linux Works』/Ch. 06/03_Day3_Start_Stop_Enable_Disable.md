# Day 3 — Start, Stop, Enable, Disable

## Lab 1: Observe current state

Use Nginx if installed:

```bash
systemctl status nginx
systemctl is-active nginx
systemctl is-enabled nginx
```

Start:

```bash
sudo systemctl start nginx
systemctl is-active nginx
```

Enable:

```bash
sudo systemctl enable nginx
systemctl is-enabled nginx
```

Stop:

```bash
sudo systemctl stop nginx
systemctl is-active nginx
systemctl is-enabled nginx
```

Disable:

```bash
sudo systemctl disable nginx
systemctl is-enabled nginx
```

Restore desired state:

```bash
sudo systemctl enable --now nginx
```

## Lab 2: Compare service and process views

```bash
systemctl status nginx
ps aux | grep nginx
ss -tulpn | grep ':80'
```

Questions:

1. Which command shows systemd’s view? systemctl status nginx
2. Which command shows process state? ps aux | grep nginx
3. Which command shows listening ports? ss -tulpn | grep ':80'
4. Does “running” prove remote clients can reach the service? No. Firewall, binding, or network issues can still block access. 

## Break/fix

Stop Nginx:

```bash
sudo systemctl stop nginx
```

From another machine:

```bash
curl -v http://app01
```

Diagnose:

```bash
systemctl status nginx
ss -tulpn | grep ':80' || true
journalctl -u nginx --since "10 minutes ago"
```

Restart:

```bash
sudo systemctl start nginx
```

## Retrieval questions

1. Difference between `start` and `enable`? `start` runs it now; `enable` makes it start at boot. 
2. Difference between `stop` and `disable`? `stop` stops it. `disable` prevents start. 
3. Difference between `restart` and `reload`? `restart` stops then starts, `reload` reloads config without full stop if supported.
4. What does `is-active` tell you? Whether the service is running. 
5. What does `is-enabled` tell you? Whether or not it's set to start at boot. 