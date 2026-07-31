# Day 8 — Review, Troubleshooting, and Exit Test

## Review diagrams

Draw from memory:

```text
firmware / bootloader
→ kernel
→ initramfs
→ PID 1
→ systemd
→ targets
→ services
→ processes
→ logs
```

And:

```text
unit file
→ systemctl daemon-reload
→ systemctl start
→ process begins
→ systemctl status
→ journalctl
```

## Mystery Challenge 1: Enabled but not running

Setup:

```bash
sudo systemctl enable hlw-ch6-web
sudo systemctl stop hlw-ch6-web
```

Question:

```text
Why does is-enabled say enabled, but curl localhost:8088 fails?
```

Tools:

```bash
systemctl is-enabled hlw-ch6-web
systemctl is-active hlw-ch6-web
systemctl status hlw-ch6-web
ss -tulpn | grep ':8088'
```

Expected explanation:

```text
Enabled means configured to start at boot. It does not mean running right now.
```

## Mystery Challenge 2: Unit changed but behavior did not

Change a unit file but do not run daemon-reload.

Expected explanation:

```text
systemd did not reread the unit file.
```

## Mystery Challenge 3: Boot log contains errors

```bash
journalctl -b -p err
```

Ask:

```text
Are all errors fatal? Which ones affected the current project?
```

Expected approach:

```text
Read the message, identify the unit, inspect status, decide whether it matters.
```

## Mystery Challenge 4: Missing executable

Break `ExecStart` as in Day 6.

Diagnose with:

```bash
systemctl status hlw-ch6-web
journalctl -u hlw-ch6-web --since "10 minutes ago"
systemctl cat hlw-ch6-web
ls -l /usr/local/bin
```

## Exit questions

Answer without notes:

1. What is user space?
2. What is PID 1?
3. What program is PID 1 on this machine?
4. What does systemd do?
5. What is a unit?
6. What is a service unit?
7. What is a target?
8. Difference between `list-units` and `list-unit-files`?
9. Difference between `start` and `enable`?
10. Difference between `stop` and `disable`?
11. Difference between `restart`, `reload`, and `daemon-reload`?
12. Why run `systemctl cat SERVICE`?
13. Why use `journalctl -u SERVICE`?
14. What does `journalctl -b` show?
15. What does `systemd-analyze blame` show?
16. Why should rescue/emergency testing require console access?
17. Where should local custom unit files usually go?
18. Why use overrides instead of editing vendor unit files?
19. How can a service fail because of file permissions?
20. How can a service fail because of a port conflict?

## Minimum completion standard

He is ready to move on when he can:

```text
create a simple service
start it
enable it
find its process
find its port
read its logs
break it three ways
diagnose each failure
undo the changes cleanly
```

If he cannot explain `enable`, `start`, `restart`, `reload`, and `daemon-reload`, he is not done.
