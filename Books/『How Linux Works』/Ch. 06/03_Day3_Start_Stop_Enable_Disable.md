# Day 3 — Start, Stop, Enable, Disable

## Core distinction

```text
start   = run now
stop    = stop now
enable  = start automatically at boot
disable = do not start automatically at boot
```

A service can be:

```text
running but disabled
stopped but enabled
running and enabled
stopped and disabled
```

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

1. Which command shows systemd’s view?
2. Which command shows process state?
3. Which command shows listening ports?
4. Does “running” prove remote clients can reach the service?

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

1. Difference between `start` and `enable`?
2. Difference between `stop` and `disable`?
3. Difference between `restart` and `reload`?
4. What does `is-active` tell you?
5. What does `is-enabled` tell you?

## Exit criterion

He can say:

```text
I can run a service now, make it start at boot, stop it now, and prevent future boot startup — and those are separate actions.
```
