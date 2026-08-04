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

1. What is PID 1? The first user-space process. 
2. What is its parent PID? 0. 
3. Which program is PID 1? Usually `systemd`. 
4. Which user owns it? `root`. 
5. Why is PID 1 special? It is the ancestor of all processes and starts/supervises the rest of user space. 

## Lab 2: Compare Ubuntu and AlmaLinux

Run on both machines:

```bash
cat /etc/os-release
ps -p 1 -o pid,comm,args
systemctl --version
```

Create:

| Topic           | Ubuntu            | AlmaLinux         |
| --------------- | ----------------- | ----------------- |
| Distribution    | Ubuntu            | AlmaLinux         |
| PID 1 command   | systemd           | systemd           |
| systemd version | varies by release | varies by release |

Questions:

1. Are both using systemd? Yes. 
2. Are the versions identical? No. 
3. Which differences come from the distribution rather than the Linux kernel? Package versions, default configs, and service names. 

## Lab 3: Inspect current boot logs

```bash
journalctl -b | less
journalctl -b -p warning
journalctl -b -p err
```

Questions:

1. What does `-b` mean? Just the current boot. 
2. Are all warnings fatal? No. 
3. Are all errors relevant to the current project? No. 
4. Why are boot logs useful after a restart? They show what failed or warned during the last startup. 

## Retrieval questions

1. What is user space? Everything outside the kernel. 
2. What is PID 1? The first user-space process started by the kernel. 
3. Why does PID 1 matter? It starts and supervises the rest of the system. 
4. What role does systemd play? It acts as PID 1:starts services, manages dependencies, collects logs. 