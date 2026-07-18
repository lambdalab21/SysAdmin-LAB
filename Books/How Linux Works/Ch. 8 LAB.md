 #Book/How-Linux-Works #drill 
Ward Ch. 8 is about the relationship between processes, the kernel, and three major hardware-resource categories: CPU, memory, and I/O. These labs should make those relationships visible rather than turn into command memorization. ([オライリー・メディア](https://www.oreilly.com/library/view/how-linux-works/9781098128913/c08.xhtml?utm_source=chatgpt.com "How Linux Works, 3rd Edition"))

# Chapter 8 lab series

## Rules for every lab

Before running a command, he should predict what will happen.

Afterward, record:

```text
Prediction:
Command:
Observed result:
Which process changed?
Which resource changed: CPU, memory, I/O, or kernel interaction?
Which tool revealed it?
What would this look like during a real outage?
```

That journal is the important part. Running commands without interpreting evidence is just copying and pasting with extra steps.

# Lab 0: Install the observation tools

On AlmaLinux:

```bash
sudo dnf install -y procps-ng sysstat lsof strace
```

Verify:

```bash
ps --version
vmstat --version
pidstat -V
lsof -v
strace -V
```

Useful division of labor:

|Tool|What it reveals|
|---|---|
|`ps`|Snapshot of process state|
|`top`|Continuously updated process activity|
|`/proc/<PID>/`|Kernel-exposed information about one process|
|`lsof`|Files and sockets opened by processes|
|`strace`|System calls and signals|
|`free`|Memory summary|
|`vmstat`|System-wide CPU, memory, paging, and I/O behavior|
|`pidstat`|Activity of individual tasks|
|`nice`, `renice`|Scheduling-priority adjustments|
|`systemd-cgls`, `systemd-cgtop`|Process grouping and cgroup resource use|

`vmstat` reports process, memory, paging, block-I/O, trap, disk, and CPU activity. `pidstat` reports statistics for individual tasks managed by the kernel. ([man7.org](https://man7.org/linux/man-pages/man8/vmstat.8.html "vmstat(8) - Linux manual page"))

---

# Lab 1: Follow a process from birth to death

## Goal

Understand:

```text
shell → child process → PID → state → signal → termination
```

## Step 1: Inspect the current shell

```bash
echo $$
ps -p $$ -o pid,ppid,user,stat,ni,comm,args
```

Inspect the parent:

```bash
PPID_OF_SHELL=$(ps -p $$ -o ppid=)
ps -p "$PPID_OF_SHELL" -o pid,ppid,user,stat,comm,args
```

## Step 2: Create a child process

```bash
sleep 600 &
SLEEP_PID=$!

echo "$SLEEP_PID"
ps -p "$SLEEP_PID" -o pid,ppid,user,stat,comm,args
```

## Step 3: Inspect the kernel’s view

```bash
cat "/proc/$SLEEP_PID/status"
```
Focus on:

```text
Name
State
Pid
PPid
Threads
VmRSS
voluntary_ctxt_switches
nonvoluntary_ctxt_switches
```

## Step 4: Terminate gracefully

```bash
kill -TERM "$SLEEP_PID"
ps -p "$SLEEP_PID"
```

## Questions

1. Why was `sleep` usually in state `S`?
    
2. What does PPID tell you?
    
3. What changed after `SIGTERM`?
    
4. Why is a sleeping process not necessarily unhealthy?
    
5. Why is PID useful during troubleshooting?
    

## Expected conclusion

A process is not merely “a program that is open.” It is a kernel-managed execution context with identity, parentage, state, memory, files, and signals.

---

# Lab 2: Compare `SIGTERM`, `SIGSTOP`, `SIGCONT`, and `SIGKILL`

## Goal

Understand that signals are different requests, not interchangeable “kill commands.”

## Step 1: Start a CPU-consuming process

```bash
yes > /dev/null &
YES_PID=$!
```

Observe:

```bash
ps -p "$YES_PID" -o pid,ppid,stat,%cpu,comm
top -p "$YES_PID"
```

Quit `top` with:

```text
q
```

## Step 2: Stop execution without terminating

```bash
kill -STOP "$YES_PID"
ps -p "$YES_PID" -o pid,stat,%cpu,comm
```

## Step 3: Resume

```bash
kill -CONT "$YES_PID"
ps -p "$YES_PID" -o pid,stat,%cpu,comm
```

## Step 4: Ask it to terminate normally

```bash
kill -TERM "$YES_PID"
ps -p "$YES_PID"
```

## Step 5: Repeat with forced termination

```bash
yes > /dev/null &
YES_PID=$!

kill -KILL "$YES_PID"
ps -p "$YES_PID"
```

## Questions

1. Did `SIGSTOP` remove the process?
    
2. What state appeared after `SIGSTOP`?
    
3. What did `SIGCONT` do?
    
4. Why should `SIGTERM` usually come before `SIGKILL`?
    
5. What cleanup opportunities might a forcibly killed application lose?
    

## Real-world connection

When a service hangs, do not reflexively use:

```bash
kill -9 <PID>
```

First ask whether a normal stop can allow logs, temporary files, or transactions to be handled cleanly.

---

# Lab 3: CPU saturation, load average, and niceness

## Goal

Understand the difference between:

```text
CPU utilization
load average
runnable processes
scheduling priority
```

## Step 1: Record baseline

```bash
nproc
uptime
vmstat 1 5
```

In `vmstat`, focus on:

```text
r
us
sy
id
wa
```

The `r` column reports runnable processes that are running or waiting for runtime. CPU columns include user time, system time, idle time, and I/O-wait time. ([man7.org](https://man7.org/linux/man-pages/man8/vmstat.8.html "vmstat(8) - Linux manual page"))

## Step 2: Create contention

Start more CPU-bound processes than the VM has virtual CPUs:

```bash
for i in $(seq 1 $(( $(nproc) + 2 ))); do
  yes > /dev/null &
done
```

Observe:

```bash
uptime
vmstat 1 10
top
```

Clean up:

```bash
pkill yes
```

## Step 3: Compare normal and low-priority work

```bash
yes > /dev/null &
NORMAL_PID=$!

nice -n 15 yes > /dev/null &
NICE_PID=$!

ps -o pid,ni,%cpu,stat,comm -p "$NORMAL_PID","$NICE_PID"
top -p "$NORMAL_PID","$NICE_PID"
```

Clean up:

```bash
kill "$NORMAL_PID" "$NICE_PID"
```

## Questions

1. How many CPUs did the VM have?
    
2. What happened to `r` when runnable work exceeded CPU capacity?
    
3. How did load average change?
    
4. Is load average the same as `%CPU`?
    
5. Which process had the larger nice value?
    
6. Does a larger nice value indicate more or less scheduling priority?
    
7. Why might priority differences be subtle on an otherwise idle VM?
    

## Extension

Run:

```bash
yes > /dev/null &
PID=$!

ps -o pid,ni,%cpu,comm -p "$PID"
renice 15 -p "$PID"
ps -o pid,ni,%cpu,comm -p "$PID"

kill "$PID"
```

Explain the difference:

```text
nice   = start with adjusted priority
renice = adjust an existing process
```

---

# Lab 4: Memory allocation and `/proc`

## Goal

Observe how a process consumes memory and how the kernel reports it.

## Step 1: Record baseline

```bash
free -h
```

## Step 2: Create a controlled memory consumer

```bash
cat > /tmp/ch8-memory.py <<'PY'
import os
import time

blocks = []

print(f"PID: {os.getpid()}", flush=True)

for step in range(1, 7):
    blocks.append(bytearray(40 * 1024 * 1024))
    print(f"Allocated approximately {step * 40} MiB", flush=True)
    time.sleep(5)

time.sleep(30)
PY
```

Run:

```bash
python3 /tmp/ch8-memory.py &
MEM_PID=$!
```

## Step 3: Observe from another terminal

```bash
watch -n 1 "
  ps -p $MEM_PID -o pid,stat,rss,vsz,%mem,comm
  echo
  grep -E '^(Name|State|VmSize|VmRSS|VmPeak|VmSwap|Threads):' /proc/$MEM_PID/status
  echo
  free -h
"
```

Stop `watch` with:

```text
Ctrl-C
```

Clean up:

```bash
kill "$MEM_PID" 2>/dev/null || true
rm /tmp/ch8-memory.py
```

## Questions

1. How did `VmRSS` change?
    
2. How did `VmSize` change?
    
3. Did `free -h` show the same number as the process’s RSS?
    
4. Why are system-level and process-level views both useful?
    
5. Was swap used?
    
6. Why should a memory experiment use small increments?
    

`/proc/<PID>/status` includes fields such as `VmSize`, `VmRSS`, `VmPeak`, and `VmSwap`, which make it useful for observing a process’s memory behavior directly. ([man7.org](https://man7.org/linux/man-pages/man5/proc_pid_status.5.html "proc_pid_status(5) - Linux manual page"))

## Real-world connection

A service can be alive and responsive at first while gradually consuming more memory. The problem may only become obvious after watching values over time.

---

# Lab 5: Observe storage I/O with `vmstat` and `pidstat`

## Goal

Distinguish CPU-heavy work from I/O-heavy work.

## Step 1: Observe idle baseline

```bash
vmstat 1 5
```

## Step 2: Start monitoring in another terminal

```bash
vmstat 1
```

In a third terminal:

```bash
pidstat -d 1
```

`pidstat -d` reports per-task I/O statistics, including disk-read and disk-write rates. ([man7.org](https://man7.org/linux/man-pages/man1/pidstat.1.html "pidstat(1) - Linux manual page"))

## Step 3: Generate controlled writes

```bash
dd if=/dev/zero of=/tmp/ch8-io-test.bin bs=1M count=512 status=progress
sync
rm /tmp/ch8-io-test.bin
```

Stop monitoring with:

```text
Ctrl-C
```

## Questions

1. What changed in `vmstat`?
    
2. What happened to `bo`?
    
3. Did `wa` noticeably increase?
    
4. Which process appeared in `pidstat -d`?
    
5. Why might results differ depending on caching, virtualized storage, and the speed of the underlying disk?
    
6. Why does `sync` matter in this experiment?
    

`vmstat` reports block input and output through `bi` and `bo`, and it reports CPU time waiting for I/O through `wa`. Its first report is based on averages since reboot; subsequent reports represent the selected sampling interval. ([man7.org](https://man7.org/linux/man-pages/man8/vmstat.8.html "vmstat(8) - Linux manual page"))

## Proxmox connection

Because this is a VM, storage behavior includes another layer:

```text
process
→ guest kernel
→ virtual disk
→ Proxmox host
→ physical storage
```

The guest can observe symptoms, but it may not reveal the complete cause.

---

# Lab 6: Open files and sockets with `lsof`

## Goal

Understand that a process interacts with files and network sockets through kernel-managed file descriptors.

## Part A: Open file

Create a file:

```bash
printf 'hello\n' > /tmp/ch8-open-file.txt
```

Open it:

```bash
less /tmp/ch8-open-file.txt
```

Leave `less` running. In another terminal:

```bash
lsof /tmp/ch8-open-file.txt
```

Quit `less`:

```text
q
```

Run again:

```bash
lsof /tmp/ch8-open-file.txt
```

## Part B: Listening socket

Start:

```bash
cd /tmp
python3 -m http.server 8000 &
WEB_PID=$!
```

Observe:

```bash
lsof -i :8000
ss -tulpn | grep ':8000'
```

Request a page:

```bash
curl http://localhost:8000
```

Clean up:

```bash
kill "$WEB_PID"
```

## Questions

1. Which process held the open text file?
    
2. Why did `lsof` stop showing it after `less` exited?
    
3. Which process listened on TCP port `8000`?
    
4. How did `lsof` and `ss` complement each other?
    
5. Why is this relevant when Nginx fails to bind to port `80`?
    
6. What error would you expect if two programs attempted to listen on the same address and port?
    

## Challenge

Leave the Python server running and try:

```bash
python3 -m http.server 8000
```

Diagnose:

```bash
lsof -i :8000
ss -tulpn | grep ':8000'
```

Do not solve a port conflict by randomly killing processes. Identify the owning process first.

---

# Lab 7: Observe the user/kernel boundary with `strace`

## Goal

See that ordinary commands ask the kernel to perform work through system calls.

The `strace` tool records system calls made by a process and signals received by a process. It is useful for diagnosis, debugging, and instruction because it exposes interactions at the user/kernel boundary. ([man7.org](https://man7.org/linux/man-pages/man1/strace.1.html "strace(1) - Linux manual page"))

## Part A: Read an existing file

```bash
strace -e trace=openat,read,close cat /etc/hostname
```

Find:

```text
openat(...)
read(...)
close(...)
```

## Part B: Request a missing file

```bash
strace -e trace=openat cat /tmp/file-that-does-not-exist
```

Look for:

```text
ENOENT
```

## Part C: Request a protected file

```bash
sudo sh -c 'printf "secret\n" > /tmp/ch8-protected.txt'
sudo chmod 000 /tmp/ch8-protected.txt
```

As a normal user:

```bash
strace -e trace=openat cat /tmp/ch8-protected.txt
```

Look for:

```text
EACCES
```

Clean up:

```bash
sudo rm /tmp/ch8-protected.txt
```

## Part D: Filter only failed system calls

```bash
strace -Z -e trace=%file cat /tmp/file-that-does-not-exist
```

`strace -Z` prints only failed system calls, while `-e trace=%file` limits tracing to system calls that reference file paths. ([man7.org](https://man7.org/linux/man-pages/man1/strace.1.html "strace(1) - Linux manual page"))

## Questions

1. What does `ENOENT` mean?
    
2. What does `EACCES` mean?
    
3. Why is the distinction useful?
    
4. How could this help diagnose an Nginx web-root problem?
    
5. How could it help diagnose a deployment script?
    
6. Why should `strace` be filtered instead of dumping everything blindly?
    

## Real-world connection

The visible symptom may be:

```text
Application failed to start
```

But `strace` may reveal:

```text
openat(... "/etc/myapp/config.ini" ...) = -1 ENOENT
```

That replaces guessing with evidence.

---

# Lab 8: Attach `strace` to a running process

## Goal

Understand the difference between starting a process under tracing and attaching to one that already exists.

## Step 1: Start a long-running process

```bash
sleep 300 &
SLEEP_PID=$!
```

## Step 2: Attach

```bash
strace -p "$SLEEP_PID"
```

Detach:

```text
Ctrl-C
```

Verify the original process still exists:

```bash
ps -p "$SLEEP_PID"
```

Clean up:

```bash
kill "$SLEEP_PID"
```

With `strace -p PID`, pressing `Ctrl-C` detaches `strace` while leaving the traced process running. ([man7.org](https://man7.org/linux/man-pages/man1/strace.1.html "strace(1) - Linux manual page"))

## Questions

1. Did detaching from `strace` terminate `sleep`?
    
2. Why is attaching useful for a running service?
    
3. Why should tracing a production process be done carefully?
    
4. What should he collect before attaching to Nginx or another real service?
    

---

# Lab 9: Processes, threads, and tasks

## Goal

Observe that one process may contain multiple threads.

Create:

```bash
cat > /tmp/ch8-threads.py <<'PY'
import os
import threading
import time

def worker(number):
    while True:
        time.sleep(1)

print(f"PID: {os.getpid()}", flush=True)

for number in range(4):
    thread = threading.Thread(target=worker, args=(number,), daemon=True)
    thread.start()

time.sleep(300)
PY
```

Run:

```bash
python3 /tmp/ch8-threads.py &
THREAD_PID=$!
```

Inspect:

```bash
ps -p "$THREAD_PID" -o pid,ppid,nlwp,stat,comm
ps -L -p "$THREAD_PID" -o pid,tid,stat,comm
grep '^Threads:' "/proc/$THREAD_PID/status"
```

Clean up:

```bash
kill "$THREAD_PID"
rm /tmp/ch8-threads.py
```

## Questions

1. What does `NLWP` show?
    
2. How many thread IDs appeared?
    
3. Did threads have separate PIDs in the ordinary process view?
    
4. Which resources do threads share?
    
5. Why can multithreading complicate troubleshooting?
    

## What he needs to retain

He does not need advanced concurrency knowledge yet. He needs to recognize:

```text
one process may contain multiple execution paths
```

---

# Lab 10: Inspect systemd cgroups without using containers

## Goal

Prepare for Docker later by observing resource grouping now.

Ward Ch. 8 introduces cgroups before containers become the focus. A cgroup is a mechanism for organizing processes hierarchically and distributing system resources in a controlled, configurable way. ([Linuxカーネルドキュメント](https://docs.kernel.org/admin-guide/cgroup-v2.html "Control Group v2 — The Linux Kernel  documentation"))

## Step 1: Inspect the current shell

```bash
cat /proc/$$/cgroup
```

## Step 2: Inspect systemd’s hierarchy

```bash
systemd-cgls
```

## Step 3: Inspect Nginx

```bash
systemctl status nginx
systemctl show nginx --property=ControlGroup
```

If Nginx is not installed or running, use SSH:

```bash
systemctl show sshd --property=ControlGroup
```

## Step 4: Watch group-level usage

```bash
systemd-cgtop
```

Quit:

```text
q
```

## Questions

1. What is a cgroup?
    
2. Why does systemd group service processes?
    
3. Why is this useful before learning containers?
    
4. How is a cgroup different from a process?
    
5. Why might a service contain more than one process?
    

## Expected conclusion

Containers do not invent process resource management from nothing. They build on kernel mechanisms that can already be observed on an ordinary Linux system.

---

# Lab 11: Diagnose a deliberately broken service

## Goal

Combine Chapter 8 tools into one troubleshooting workflow.

## Setup

Create a simple systemd service.

```bash
sudo tee /usr/local/bin/ch8-web.sh > /dev/null <<'SH'
#!/usr/bin/env bash
exec python3 -m http.server 8080 --directory /srv/ch8-web
SH

sudo chmod 755 /usr/local/bin/ch8-web.sh
sudo mkdir -p /srv/ch8-web
echo '<h1>Chapter 8 Lab</h1>' | sudo tee /srv/ch8-web/index.html > /dev/null
```

Create unit:

```bash
sudo tee /etc/systemd/system/ch8-web.service > /dev/null <<'UNIT'
[Unit]
Description=Chapter 8 web lab
After=network.target

[Service]
ExecStart=/usr/local/bin/ch8-web.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
UNIT
```

Start:

```bash
sudo systemctl daemon-reload
sudo systemctl start ch8-web
```

Verify:

```bash
curl http://localhost:8080
```

## Break it

Start a conflicting process:

```bash
sudo systemctl stop ch8-web
python3 -m http.server 8080 --directory /tmp &
CONFLICT_PID=$!
sudo systemctl start ch8-web
```

## Diagnose

Use:

```bash
systemctl status ch8-web
journalctl -u ch8-web --since "5 minutes ago"
ss -tulpn | grep ':8080'
lsof -i :8080
ps -fp "$CONFLICT_PID"
```

Clean up:

```bash
kill "$CONFLICT_PID"
sudo systemctl restart ch8-web
curl http://localhost:8080
```

## Questions

1. Was the machine down?
    
2. Was networking broken?
    
3. Did the service fail before or after starting?
    
4. Which process occupied port `8080`?
    
5. Which tool gave the fastest useful clue?
    
6. What evidence appeared in the journal?
    
7. Why is random firewall editing the wrong response?
    

## Lesson

A failed web request is not automatically a networking problem.

This lab creates the exact habit he will need in Ward Ch. 9–10:

```text
host
→ process
→ port
→ firewall
→ application
→ logs
```

---

# Lab 12: Final mystery challenge

Do not tell him which failure you created.

Choose one:

## Mystery A: CPU saturation

```bash
for i in $(seq 1 $(( $(nproc) + 3 ))); do
  yes > /dev/null &
done
```

## Mystery B: Memory growth

Run the memory script from Lab 4.

## Mystery C: Port conflict

```bash
python3 -m http.server 8080 --directory /tmp &
```

Then restart `ch8-web`.

## Mystery D: Missing file

```bash
sudo mv /srv/ch8-web/index.html /srv/ch8-web/index.html.bak
```

## Mystery E: Permission problem

```bash
sudo chmod 000 /srv/ch8-web/index.html
```

## Mystery F: Stopped service

```bash
sudo systemctl stop ch8-web
```

He should diagnose without random changes.

Expected workflow:

```bash
uptime
top
free -h
vmstat 1 5
systemctl status ch8-web
journalctl -u ch8-web --since "10 minutes ago"
ss -tulpn
lsof -i :8080
curl -v http://localhost:8080
```

Use `strace` only if the evidence still points toward file access or another system-call-level problem.

# Required written summary

After completing the labs, have him write one page answering:

```text
1. How does the kernel manage processes?
2. What is the difference between a program, process, service, and thread?
3. How do ps, top, vmstat, pidstat, lsof, and strace differ?
4. What evidence suggests CPU pressure?
5. What evidence suggests memory pressure?
6. What evidence suggests I/O pressure?
7. How do you identify which process owns a listening port?
8. Why is a running machine not the same as a working service?
9. Why should strace be used after simpler tools?
10. How do cgroups connect systemd services to containers learned later?
```

# Exit criteria before Ward Ch. 9

He is ready to move into networking when he can perform these tasks without instructions:

```bash
# Identify a process and its parent
ps -p <PID> -o pid,ppid,stat,comm,args

# Inspect kernel-exposed process information
cat /proc/<PID>/status

# Find a CPU-heavy process
top

# Read system-wide resource pressure
vmstat 1 5

# Observe one process over time
pidstat -p <PID> 1 5

# Find the owner of a listening port
ss -tulpn
lsof -i :<PORT>

# Distinguish missing file from permission denial
strace -Z -e trace=%file <COMMAND>

# Inspect systemd process grouping
systemctl show <SERVICE> --property=ControlGroup
```

He does not need to memorize every `vmstat` column before proceeding.

He does need to understand the troubleshooting sequence:

```text
observe
→ form a hypothesis
→ inspect the correct layer
→ make one controlled change
→ verify
→ undo temporary changes
```

That is the real value of Ch. 8.