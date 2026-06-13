More natural phrasing: **“Create drills to retain the information in _How Linux Works_ Ch. 8.”**

Ward Ch. 8 is **“A Closer Look at Processes and Resource Utilization.”** It focuses on the relationship between processes, the kernel, and three basic hardware-resource categories: CPU, memory, and I/O. ([オライリー・メディア](https://www.oreilly.com/library/view/how-linux-works/9781098128913/c08.xhtml?utm_source=chatgpt.com "How Linux Works, 3rd Edition"))

Do not turn this into another long chapter. Use short drills repeatedly on `app01` or another disposable VM.

# Preparation

On AlmaLinux:

```bash
sudo dnf install -y lsof strace sysstat procps-ng
```

Useful tools:

|Tool|Main purpose|
|---|---|
|`ps`|Take a snapshot of processes|
|`top`|Watch processes and resource use interactively|
|`lsof`|Show files and sockets opened by processes|
|`strace`|Observe system calls made by a process|
|`free`|Inspect memory usage|
|`vmstat`|Observe CPU, memory, paging, and I/O activity|
|`pidstat`|Observe resource use by individual processes|
|`nice`, `renice`|Adjust process scheduling priority|

# Daily 10-minute drill

Repeat this several times over two weeks.

## Drill 1: Find your shell process

Run:

```bash
echo $$
ps -p $$ -o pid,ppid,user,stat,comm,args
```

Answer:

1. What is `$$`?
    
2. What is the PID?
    
3. What is the PPID?
    
4. What program is running?
    
5. What process started the shell?
    

Then inspect the parent:

```bash
ps -p "$(ps -p $$ -o ppid=)" -o pid,ppid,user,stat,comm,args
```

### What he should retain

- A program is code on disk.
    
- A process is a running instance of a program.
    
- Every process has a PID.
    
- Most processes have a parent process.
    

---

## Drill 2: Create and terminate a process

Start a background process:

```bash
sleep 300 &
```

The shell prints a job number and PID.

Inspect it:

```bash
jobs -l
ps -p "$!" -o pid,ppid,stat,comm,args
```

Terminate it politely:

```bash
kill -TERM "$!"
```

Verify:

```bash
ps -p "$!"
```

Repeat, but force termination:

```bash
sleep 300 &
kill -KILL "$!"
```

### Questions

1. What does `&` do?
    
2. What does `$!` contain?
    
3. What is the difference between `SIGTERM` and `SIGKILL`?
    
4. Why should `SIGTERM` usually be tried first?
    

### Expected understanding

- `SIGTERM` asks a process to terminate cleanly.
    
- `SIGKILL` forces the kernel to terminate it.
    
- A process cannot catch or ignore `SIGKILL`.
    

---

## Drill 3: Identify sleeping and running processes

Run:

```bash
sleep 300 &
yes > /dev/null &
ps -o pid,ppid,stat,comm -p "$!"
```

The second background process consumes CPU. Find both processes:

```bash
ps -C sleep -o pid,ppid,stat,comm,args
ps -C yes   -o pid,ppid,stat,comm,args
```

Then inspect with:

```bash
top
```

Stop `top` with:

```text
q
```

Terminate `yes`:

```bash
pkill yes
```

### Questions

1. Why is `sleep` normally sleeping rather than running?
    
2. Why does `yes` consume CPU?
    
3. Is a sleeping process necessarily broken?
    
4. Which process rises near the top of `top`?
    

### Lesson

A sleeping process is often healthy. It may simply be waiting for time, input, I/O, or another event.

---

# CPU experiments

## Experiment 1: Observe CPU competition

Run:

```bash
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
```

Inspect:

```bash
top
```

In another terminal:

```bash
uptime
nproc
```

Clean up:

```bash
pkill yes
```

### Questions

1. How many CPU cores does the machine have?
    
2. How does the number of CPU-heavy processes compare with the number of cores?
    
3. What happens to load average?
    
4. Does load average equal CPU percentage?
    

### Lesson

Load average is not the same thing as CPU percentage. Interpret load relative to the number of available CPU cores.

---

## Experiment 2: Use `nice`

Start two CPU-heavy processes:

```bash
nice -n 0 yes > /dev/null &
PID_NORMAL=$!

nice -n 15 yes > /dev/null &
PID_NICE=$!
```

Inspect:

```bash
ps -o pid,ni,stat,%cpu,comm -p "$PID_NORMAL","$PID_NICE"
top
```

Clean up:

```bash
kill "$PID_NORMAL" "$PID_NICE"
```

### Questions

1. What does the `NI` column show?
    
2. Which process has the larger nice value?
    
3. Does a higher nice value mean higher or lower scheduling priority?
    
4. Why is the command called `nice`?
    

### Lesson

A “nicer” process is more willing to yield CPU time to other work.

---

## Experiment 3: Change priority after launch

Run:

```bash
yes > /dev/null &
PID=$!

ps -o pid,ni,%cpu,comm -p "$PID"
renice 15 -p "$PID"
ps -o pid,ni,%cpu,comm -p "$PID"

kill "$PID"
```

### Questions

1. What is the difference between `nice` and `renice`?
    
2. Which command changes priority before launch?
    
3. Which command changes priority after launch?
    

---

# Memory drills

## Drill 4: Read memory output correctly

Run:

```bash
free -h
```

Answer:

1. How much total RAM exists?
    
2. How much memory is used?
    
3. What does `available` mean?
    
4. Is a low `free` number automatically bad?
    
5. Is swap being used?
    

### Lesson

Linux uses otherwise-idle memory for caches and buffers. Do not panic merely because raw “free” memory is low. `available` is usually more useful.

---

## Experiment 4: Observe memory allocation safely

Create a temporary Python script:

```bash
cat > /tmp/use-memory.py <<'PY'
import time

blocks = []
for _ in range(5):
    blocks.append(bytearray(50 * 1024 * 1024))
    print(f"Allocated approximately {len(blocks) * 50} MiB")
    time.sleep(5)

time.sleep(30)
PY
```

Run it:

```bash
python3 /tmp/use-memory.py &
PID=$!
```

Observe in another terminal:

```bash
watch -n 1 "ps -o pid,rss,vsz,%mem,comm -p $PID; echo; free -h"
```

Stop `watch` with:

```text
Ctrl-C
```

Clean up:

```bash
kill "$PID" 2>/dev/null || true
rm /tmp/use-memory.py
```

### Questions

1. What does RSS represent?
    
2. What does VSZ represent?
    
3. How does `free -h` change while the process allocates memory?
    
4. Why should you avoid allocating huge amounts on a shared server?
    

### Lesson

Use controlled experiments. Do not try to crash the machine to prove that RAM is finite.

---

# I/O and `vmstat`

## Drill 5: Read `vmstat`

Run:

```bash
vmstat 1 5
```

Pay attention to:

```text
r  b  swpd  free  buff  cache  si  so  bi  bo  us  sy  id  wa
```

He does not need to memorize everything immediately.

Start with:

|Column|Meaning|
|---|---|
|`r`|Runnable processes waiting for CPU|
|`b`|Processes blocked while waiting|
|`si`, `so`|Swap-in and swap-out activity|
|`bi`, `bo`|Block-device input and output|
|`us`|CPU time used by user-space work|
|`sy`|CPU time used by kernel work|
|`id`|Idle CPU time|
|`wa`|CPU time waiting on I/O|

### Questions

1. Why can the first line differ from the later lines?
    
2. Which columns would suggest swap activity?
    
3. Which column may suggest storage-related waiting?
    
4. What does a high `id` value mean?
    

### Lesson

The first `vmstat` line commonly reflects averages since boot. Later lines reflect the requested sampling interval.

---

## Experiment 5: Observe disk writes

Create a temporary file:

```bash
dd if=/dev/zero of=/tmp/io-test.bin bs=1M count=256 status=progress
sync
rm /tmp/io-test.bin
```

While running it, inspect from another terminal:

```bash
vmstat 1
```

Optionally:

```bash
iostat -xz 1
```

Stop with:

```text
Ctrl-C
```

### Questions

1. Which `vmstat` columns changed?
    
2. What did `sync` do?
    
3. Why is `/tmp` only acceptable for a controlled lab?
    
4. Why would a larger test be inappropriate on a production server?
    

---

# `lsof` drills

## Drill 6: Observe an open file

Create a file:

```bash
printf 'test\n' > /tmp/open-file.txt
```

Open it with `less`:

```bash
less /tmp/open-file.txt
```

Leave `less` open. In another terminal:

```bash
lsof /tmp/open-file.txt
```

Then quit `less`:

```text
q
```

Run again:

```bash
lsof /tmp/open-file.txt
```

### Questions

1. What changed?
    
2. Which process had the file open?
    
3. Why can an open file matter during troubleshooting?
    

---

## Drill 7: Find the process listening on a port

Start a local web server:

```bash
cd /tmp
python3 -m http.server 8000 &
PID=$!
```

Inspect:

```bash
lsof -i :8000
ss -tulpn | grep ':8000'
```

Test:

```bash
curl http://localhost:8000
```

Clean up:

```bash
kill "$PID"
```

### Questions

1. Which process listened on TCP port `8000`?
    
2. How are `lsof` and `ss` different?
    
3. What does it mean for a process to listen on a port?
    
4. Why does a running process not necessarily mean the expected port is open?
    

### Lesson

This drill connects Ch. 8 directly to Ward Ch. 9–10.

---

# `strace` drills

## Drill 8: Trace a successful file read

Run:

```bash
strace -e trace=openat,read,close cat /etc/hostname
```

### Questions

1. Which system call opens the file?
    
2. Which system call reads bytes?
    
3. Which system call closes the file?
    
4. Why does a user-space process need system calls?
    

### Lesson

A process cannot directly perform privileged kernel operations. It requests services through system calls.

---

## Drill 9: Trace a missing file

Run:

```bash
strace -e trace=openat cat /tmp/this-file-does-not-exist
```

Look for:

```text
ENOENT
```

### Questions

1. What does `ENOENT` indicate?
    
2. How does `strace` reveal the actual path the program tried to open?
    
3. Why is this better than guessing?
    

---

## Drill 10: Trace a permission error

Create a protected file:

```bash
sudo sh -c 'printf "secret\n" > /tmp/protected-file.txt'
sudo chmod 000 /tmp/protected-file.txt
```

As a normal user:

```bash
strace -e trace=openat cat /tmp/protected-file.txt
```

Look for:

```text
EACCES
```

Restore and remove:

```bash
sudo chmod 644 /tmp/protected-file.txt
sudo rm /tmp/protected-file.txt
```

### Questions

1. What does `EACCES` indicate?
    
2. How is `EACCES` different from `ENOENT`?
    
3. Why is this relevant to the earlier `scp` permission failure?
    

### Lesson

`strace` can show whether a failure comes from a missing file or denied permission.

---

## Drill 11: Attach `strace` to an existing process

Start:

```bash
sleep 60 &
PID=$!
```

Attach:

```bash
strace -p "$PID"
```

Detach without killing the process:

```text
Ctrl-C
```

Check:

```bash
ps -p "$PID"
```

Clean up:

```bash
kill "$PID"
```

### Questions

1. Did pressing `Ctrl-C` kill `sleep`?
    
2. What did it stop?
    
3. What does attaching to an existing process mean?
    
4. Why should tracing be used cautiously on a busy server?
    

---

# `pidstat` drill

## Drill 12: Observe one process over time

Start:

```bash
yes > /dev/null &
PID=$!
```

Observe:

```bash
pidstat -p "$PID" 1 5
```

Then:

```bash
kill "$PID"
```

Repeat with I/O:

```bash
dd if=/dev/zero of=/tmp/pidstat-test.bin bs=1M count=256 status=none &
PID=$!

pidstat -d -p "$PID" 1 5
rm -f /tmp/pidstat-test.bin
```

### Questions

1. How is `pidstat` different from `vmstat`?
    
2. Which tool gives system-wide information?
    
3. Which tool helps isolate one process?
    
4. When would `pidstat` be more useful than `top`?
    

---

# Threads drill

## Drill 13: Inspect threads

Choose a running multithreaded process:

```bash
ps -eLf | less
```

Or inspect a specific PID:

```bash
ps -L -p <PID> -o pid,tid,psr,stat,comm
```

### Questions

1. What is a thread?
    
2. How is a thread different from a process?
    
3. Why can a multithreaded program use multiple CPU cores?
    
4. Why can multithreaded programs be harder to debug?
    

Do not over-focus on threads yet. He only needs the mental model.

---

# Cgroups drill

## Drill 14: Inspect cgroup membership

For the current shell:

```bash
cat /proc/$$/cgroup
```

For Nginx:

```bash
systemctl status nginx
systemctl show nginx --property=ControlGroup
```

Inspect:

```bash
systemd-cgls
```

### Questions

1. What is a cgroup?
    
2. Why does systemd place services into cgroups?
    
3. Why are cgroups relevant to containers later?
    
4. Why should he learn cgroups before Docker?
    

### Lesson

Containers are not magic. Cgroups are one of the kernel mechanisms used to organize, measure, and limit groups of processes.

---

# Five-minute recall drills

These should be done without notes.

## Drill A: Tool matching

Match the tool to the task.

|Task|Tool|
|---|---|
|Find a process by PID|`ps`|
|Watch resource use interactively|`top`|
|Identify which process owns port `8000`|`lsof` or `ss`|
|Observe system calls|`strace`|
|Check memory and swap|`free`|
|Observe system-wide activity repeatedly|`vmstat`|
|Observe one process repeatedly|`pidstat`|
|Start with lower CPU priority|`nice`|
|Change priority after launch|`renice`|
|Inspect process groups managed by systemd|`systemd-cgls`|

Repeat until he can answer immediately.

## Drill B: Explain the difference

Ask him to explain each pair in one or two sentences:

```text
program vs process
process vs thread
running vs sleeping
PID vs PPID
elapsed time vs CPU time
free memory vs available memory
vmstat vs pidstat
lsof vs strace
nice vs renice
SIGTERM vs SIGKILL
```

## Drill C: Diagnose the symptom

Ask which tool he would try first.

|Symptom|First useful tool|
|---|---|
|Website is slow|`top`, then `vmstat`|
|Port `8000` is already taken|`ss` or `lsof`|
|Program says config file missing|`strace`|
|Server is swapping heavily|`free`, then `vmstat`|
|One process may be writing heavily to disk|`pidstat -d`|
|Nginx appears to be running but site fails|`systemctl`, `ss`, `lsof`, logs|
|Need to see service grouping|`systemctl show`, `systemd-cgls`|

# Weekly troubleshooting challenge

Have him complete this without being told the answer.

## Challenge 1: Mystery CPU usage

Create the problem:

```bash
yes > /dev/null &
```

Ask him to identify and terminate the offending process.

Expected tools:

```bash
top
ps
kill
```

## Challenge 2: Mystery occupied port

Create the problem:

```bash
python3 -m http.server 8000 &
```

Ask him:

> Why can another program not listen on port `8000`?

Expected tools:

```bash
ss -tulpn
lsof -i :8000
```

## Challenge 3: Mystery missing file

Create:

```bash
cat > /tmp/read-config.py <<'PY'
with open("/tmp/app-config.txt") as f:
    print(f.read())
PY
```

Run:

```bash
python3 /tmp/read-config.py
```

Ask him to diagnose with:

```bash
strace -e trace=openat python3 /tmp/read-config.py
```

Fix:

```bash
printf 'configured\n' > /tmp/app-config.txt
python3 /tmp/read-config.py
```

## Challenge 4: Mystery permission error

Create:

```bash
sudo mkdir -p /tmp/ch8-private
sudo touch /tmp/ch8-private/data.txt
sudo chmod 700 /tmp/ch8-private
```

Ask him why his normal account cannot read:

```bash
cat /tmp/ch8-private/data.txt
```

Useful tools:

```bash
ls -ld /tmp/ch8-private
namei -l /tmp/ch8-private/data.txt
strace -e trace=openat cat /tmp/ch8-private/data.txt
```

Clean up:

```bash
sudo rm -rf /tmp/ch8-private
```

# Retention schedule

Use this schedule rather than doing all drills once.

| Day    | Work                                                   |
| ------ | ------------------------------------------------------ |
| Day 1  | Drills 1–3: PIDs, jobs, signals, `top`                 |
| Day 2  | CPU experiments: `yes`, load average, `nice`, `renice` |
| Day 3  | Memory: `free`, controlled Python allocation           |
| Day 4  | I/O: `vmstat`, `iostat`, safe `dd` test                |
| Day 5  | `lsof`, sockets, occupied-port experiment              |
| Day 6  | `strace`: success, missing file, permission denied     |
| Day 7  | No commands: verbal recall only                        |
| Day 9  | Repeat tool-matching drill and one mystery challenge   |
| Day 12 | Repeat two random experiments                          |
| Day 16 | Diagnose all four mystery challenges without notes     |
| Day 30 | Five-minute verbal review plus one random challenge    |

# Exit criteria before Ward Ch. 9

He is ready to move into networking when he can answer these without notes:

1. What is a process?
    
2. What do PID and PPID represent?
    
3. Why is a sleeping process often normal?
    
4. Difference between `SIGTERM` and `SIGKILL`?
    
5. Difference between `ps` and `top`?
    
6. What does `lsof` reveal?
    
7. What does `strace` reveal?
    
8. Why is low raw free memory not automatically bad?
    
9. What does `vmstat` show?
    
10. Difference between `vmstat` and `pidstat`?
    
11. What does niceness affect?
    
12. What is a cgroup?
    
13. How do you identify the process listening on TCP port `8000`?
    
14. How do you distinguish “missing file” from “permission denied” with `strace`?
    

He does not need perfect recall of every column in every command. He needs to know:

```text
Which tool should I use next, and what evidence am I looking for?
```

That is the Ch. 8 habit that will carry into networking, Nginx, SSH, deployment, Docker later, and production troubleshooting.