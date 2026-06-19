A cleaner phrasing is: “Please make short drills he can practice regularly so that he retains what he learned in Chapter 10.”

Use `playground01`. Each drill set should take about 3–7 minutes. Rotate them. Do not do the full Chapter 10 lab every time.

# Chapter 10 Short Drills: Processes

## One-time setup

Create a practice directory:

```bash
mkdir -p ~/ch10-drills
cd ~/ch10-drills
```

---

# Drill Set A: Inspect the shell and processes

Run:

```bash
echo "$$"
ps
ps -o pid,ppid,stat,ni,cmd
```

Then say aloud:

```text
PID  = process ID
PPID = parent process ID
STAT = process state
NI   = nice value
```

Repeat twice.

---

# Drill Set B: Foreground process and `Ctrl-c`

Run:

```bash
sleep 60
```

While it is running, press:

```text
Ctrl-c
```

Then confirm:

```bash
pgrep -a sleep
```

Expected result: no `sleep` process remains.

Repeat twice.

---

# Drill Set C: Background process

Run:

```bash
sleep 120 &
jobs -l
pgrep -a sleep
```

Say aloud:

```text
[1]      = shell job number
PID      = operating-system process ID
&        = run in background
jobs -l  = show shell jobs with PIDs
```

Clean up:

```bash
kill "$!"
```

---

# Drill Set D: Pause and resume

Run:

```bash
sleep 120
```

Press:

```text
Ctrl-z
```

Then run:

```bash
jobs -l
bg %1
jobs -l
fg %1
```

Stop it:

```text
Ctrl-c
```

Repeat once more.

---

# Drill Set E: Use `kill`

Run:

```bash
sleep 300 &
PID=$!
echo "$PID"
```

Pause it:

```bash
kill -STOP "$PID"
ps -o pid,stat,cmd -p "$PID"
```

Resume it:

```bash
kill -CONT "$PID"
ps -o pid,stat,cmd -p "$PID"
```

Terminate it normally:

```bash
kill "$PID"
ps -p "$PID"
```

Say aloud:

```text
STOP = pause
CONT = continue
TERM = ask process to terminate
```

---

# Drill Set F: Avoid careless `kill -9`

Run:

```bash
sleep 300 &
PID=$!
```

Terminate normally:

```bash
kill "$PID"
```

Say aloud:

```text
Use plain kill first.
Use kill -9 only when graceful termination fails.
```

Then run another disposable process:

```bash
sleep 300 &
PID=$!
kill -9 "$PID"
```

This second command is only to remember the syntax.

---

# Drill Set G: Inspect CPU usage with `top`

Start a disposable CPU-intensive process:

```bash
yes > /dev/null &
YES_PID=$!
```

Run:

```bash
top
```

Find `yes`, then quit:

```text
q
```

Terminate it:

```bash
kill "$YES_PID"
```

Verify:

```bash
pgrep -a yes
```

---

# Drill Set H: Compare `ps`, `pgrep`, and `jobs`

Run:

```bash
sleep 100 &
sleep 200 &
```

Inspect with three commands:

```bash
jobs -l
pgrep -a sleep
ps -o pid,ppid,stat,cmd -C sleep
```

Clean up:

```bash
killall sleep
```

Say aloud:

```text
jobs  = jobs from this shell
pgrep = find processes by name
ps    = inspect process details
```

---

# Drill Set I: Parent and child processes

Run:

```bash
bash
```

Inside the child shell:

```bash
echo "PID=$$"
echo "PPID=$PPID"
ps -o pid,ppid,cmd -p "$$" -p "$PPID"
exit
```

Then say aloud:

```text
A shell can start another shell.
The new shell is a child process.
```

---

# Drill Set J: `$!` practice

Run:

```bash
sleep 180 &
echo "$!"
```

Then:

```bash
PID=$!
ps -p "$PID"
kill "$PID"
```

Say aloud:

```text
$! = PID of the most recently started background process
```

---

# Drill Set K: `nice`

Run:

```bash
nice -n 10 sleep 180 &
PID=$!
ps -o pid,ni,cmd -p "$PID"
```

Change the nice value:

```bash
renice 15 -p "$PID"
ps -o pid,ni,cmd -p "$PID"
```

Clean up:

```bash
kill "$PID"
```

Say aloud:

```text
nice   = start with adjusted priority
renice = change priority of an existing process
higher nice value = lower priority
```

---

# Drill Set L: `nohup`

Run:

```bash
nohup sleep 180 > ~/nohup-test.log 2>&1 &
PID=$!
echo "$PID"
```

Inspect:

```bash
ps -p "$PID"
cat ~/nohup-test.log
```

Clean up:

```bash
kill "$PID"
rm -f ~/nohup-test.log
```

Say aloud:

```text
nohup helps a process survive terminal logout
```

---

# Drill Set M: `killall`

Run:

```bash
sleep 100 &
sleep 200 &
sleep 300 &
```

Inspect:

```bash
pgrep -a sleep
```

Terminate all matching processes:

```bash
killall sleep
```

Verify:

```bash
pgrep -a sleep
```

Say aloud:

```text
kill PID     = target one process
killall name = target processes by name
```

---

# Drill Set N: Process ownership

Run:

```bash
sleep 180 &
PID=$!
ps -o user,pid,ppid,cmd -p "$PID"
kill "$PID"
```

Then inspect a root-owned process:

```bash
ps aux | grep '[s]shd'
```

Say aloud:

```text
A regular user can normally manage their own processes.
Root-owned processes usually require sudo.
```

---

# Two-minute rapid drill

Use this when there is little time:

```bash
sleep 60 &
PID=$!

jobs -l
ps -o pid,ppid,stat,cmd -p "$PID"
kill -STOP "$PID"
kill -CONT "$PID"
kill "$PID"
ps -p "$PID"
```

---

# Five-minute daily rotation

|Day|Drill sets|
|---|---|
|Monday|A, B, C|
|Tuesday|D, E|
|Wednesday|G, H|
|Thursday|I, J|
|Friday|K, L|
|Saturday|M, N|
|Sunday|Two-minute rapid drill|

---

# Commands he should retain

```bash
ps
top
pgrep
jobs
fg
bg
kill
killall
nice
renice
nohup
```

Keyboard shortcuts:

```text
Ctrl-c = interrupt
Ctrl-z = pause
```

Shell variables:

```text
$$  = current shell PID
$!  = most recent background PID
PPID = parent PID
```

The habit that matters most is:

```text
inspect
→ identify PID
→ understand the process
→ choose the least destructive action
→ verify
```
