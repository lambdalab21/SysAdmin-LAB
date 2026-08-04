# How Linux Works Ch. 6 — How User Space Starts

## Purpose

This guide turns Ch. 6 into practical Linux administration practice.

The student should understand this chain:

```text
firmware / bootloader
→ kernel
→ initramfs
→ PID 1
→ systemd
→ targets
→ services
→ login or network services
```

The goal is not boot trivia. The goal is to understand how a Linux machine transitions from “kernel started” to “services are running.”

## Core ideas

- The kernel starts the first user-space process.
- PID 1 is special.
- On most modern Linux distributions, PID 1 is `systemd`.
- `systemd` starts and manages units.
- A service can be started now without being enabled at boot.
- Unit files describe how services, targets, mounts, sockets, and timers are managed.
- Logs reveal what happened during boot and service startup.

## Safety rule

Do not experiment with rescue mode, emergency mode, boot targets, bootloader settings, or initramfs on an important machine.

Use:

```text
disposable VM
snapshot before risky experiments
Proxmox console access
```

## Commands to know

```bash
ps -p 1 -o pid,comm,args
systemctl status
systemctl list-units
systemctl list-unit-files
systemctl cat
systemctl show
systemctl start
systemctl stop
systemctl restart
systemctl reload
systemctl enable
systemctl disable
systemctl daemon-reload
systemctl get-default
journalctl -b
journalctl -u SERVICE
systemd-analyze
systemd-analyze blame
systemd-analyze critical-chain
```

## Study method

For each session:

1. Read the assigned section.
2. Predict what a command will show.
3. Run it.
4. Explain which layer is being inspected.
5. Make one controlled change.
6. Verify.
7. Undo temporary changes.

## Final target

He should be able to explain:

```text
PID 1 is the first user-space process.
systemd usually runs as PID 1.
systemd starts services from unit files.
start/stop affect now.
enable/disable affect boot behavior.
daemon-reload rereads unit files.
journalctl and systemctl status provide evidence.
```
