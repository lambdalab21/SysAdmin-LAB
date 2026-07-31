# Day 1 — From Kernel to PID 1

## Read before lab

Read the beginning of Ch. 6 on how user space starts.

Focus on:

```text
kernel has started
→ kernel starts first user-space process
→ PID 1 starts the rest of user space
```

## Lab 1: Identify PID 1

Run:

```bash
ps -p 1 -o pid,ppid,user,stat,comm,args
cat /proc/1/comm
cat /proc/1/status | head -n 20
```

Answer:

1. What is PID 1?
2. What is its parent PID?
3. Which program is PID 1?
4. Which user owns it?
5. Why is PID 1 special?

## Lab 2: Compare Ubuntu and AlmaLinux

Run on both machines:

```bash
cat /etc/os-release
ps -p 1 -o pid,comm,args
systemctl --version
```

Create:

| Topic | Ubuntu | AlmaLinux |
|---|---|---|
| Distribution | | |
| PID 1 command | | |
| systemd version | | |

Questions:

1. Are both using systemd?
2. Are the versions identical?
3. Which differences come from the distribution rather than the Linux kernel?

## Lab 3: Inspect current boot logs

```bash
journalctl -b | less
journalctl -b -p warning
journalctl -b -p err
```

Questions:

1. What does `-b` mean?
2. Are all warnings fatal?
3. Are all errors relevant to the current project?
4. Why are boot logs useful after a restart?

## Retrieval questions

1. What is user space?
2. What is PID 1?
3. Why does PID 1 matter?
4. What role does systemd play?
5. Where can you inspect messages from the current boot?

## Exit criterion

He can say:

```text
The kernel starts PID 1. PID 1 starts and supervises the rest of the user-space system.
```
