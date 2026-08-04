# Day 2 — systemd Units and Unit Files

## Read before lab

Read the Ch. 6 sections on systemd and unit files.

## Concept

A systemd unit is something systemd knows how to manage.

| Unit type | Example | Purpose |
|---|---|---|
| `.service` | `nginx.service` | service/daemon |
| `.socket` | `sshd.socket` | socket activation |
| `.target` | `multi-user.target` | grouping/synchronization point |
| `.timer` | `dnf-makecache.timer` | scheduled activation |
| `.mount` | generated mount unit | filesystem mount |

## Lab 1: List active units

```bash
systemctl list-units
systemctl list-units --type=service
systemctl list-units --type=target
systemctl list-units --type=timer
```

Questions:

1. What is an active unit? An active unit is a systemd unit loaded in an active state. 
2. Which services are running? The running services are the ones shown as `active`. 
3. Which targets are active?
4. Which timers exist?

## Lab 2: List unit files

```bash
systemctl list-unit-files --type=service | less
systemctl list-unit-files --type=target | less
```

Questions:

1. What is the difference between `list-units` and `list-unit-files`?
2. What does `enabled` mean?
3. What does `disabled` mean?
4. Can a disabled service still be running now?

## Lab 3: Inspect SSH or Nginx unit

AlmaLinux SSH:

```bash
systemctl status sshd
systemctl cat sshd
systemctl show sshd --property=FragmentPath
```

Nginx:

```bash
systemctl status nginx
systemctl cat nginx
systemctl show nginx --property=FragmentPath
```

Questions:

1. Where is the unit file?
2. What command starts the service?
3. Is there an `ExecReload` command?
4. What target wants this service?
5. What dependencies are listed?

## Lab 4: Unit file locations

```bash
ls /usr/lib/systemd/system | head
ls /etc/systemd/system | head
```

On Ubuntu, also try:

```bash
ls /lib/systemd/system | head
```

Questions:

1. Which directory contains vendor/distribution unit files?
2. Which directory contains local admin units or overrides?
3. Why avoid editing vendor unit files directly?

## Exit criterion

He can say:

```text
A unit file describes how systemd should manage a service or another resource.
```
