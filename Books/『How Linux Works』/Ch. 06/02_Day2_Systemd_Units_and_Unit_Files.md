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
3. Which targets are active? The active targets are the ones shown as active in systemctl list-units --type=target. 
4. Which timers exist? The timers are the units listed by systemctl list-units --type=timer. 

## Lab 2: List unit files

```bash
systemctl list-unit-files --type=service | less
systemctl list-unit-files --type=target | less
```

Questions:

1. What is the difference between `list-units` and `list-unit-files`? list-units shows units currently loaded and their active state; list-unit-files shows installed unit files and whether they're enabled or disabled. 
2. What does `enabled` mean? enabled means the service is configured to start automatically at boot or when it's triggered.  
3. What does `disabled` mean? It's not set to start automatically. 
4. Can a disabled service still be running now? Yes. 

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

1. Where is the unit file? The unit file is at the path shown by FragmentPath. 
2. What command starts the service? The service starts with the command in the ExecStart line. 
3. Is there an `ExecReload` command? Yes. 
4. What target wants this service? The target that wants it is shown in the unit file under `WantedBy=`.  
5. What dependencies are listed? The listed dependencies are the Requires=, Wants=, After=, Before=, and similar directives. 

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

1. Which directory contains vendor/distribution unit files? /usr/lib/systemd/system contains the vendor/distribution unit files. 
2. Which directory contains local admin units or overrides? /etc/systemd/system contains local admin units and overrides. 
3. Why avoid editing vendor unit files directly? Because vendor files may be overwritten by packages. 