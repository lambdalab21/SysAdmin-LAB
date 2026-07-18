# The Linux Command Line, Chapters 1–10: Summer Retention Drills

Use these on `playground01`. Repeat weekly through the summer. Keep each session short: 20–35 minutes.

Rules: predict first, run the command, explain the output aloud, clean up, and do not use the mouse.

## One-time setup

```bash
mkdir -p ~/tlcl-summer-drills/{alpha,beta,gamma,logs,bin,tmp}
cd ~/tlcl-summer-drills
printf 'apple\nbanana\napple\norange\n' > alpha/fruits.txt
printf 'red\nblue\ngreen\nred\n' > beta/colors.txt
printf 'error: failed login\ninfo: started\nerror: timeout\n' > logs/app.log
echo '#!/usr/bin/env bash' > bin/hello.sh
echo 'echo "hello from $0"' >> bin/hello.sh
chmod 755 bin/hello.sh
touch alpha/a.txt alpha/b.txt beta/c.txt gamma/d.md tmp/junk.tmp
find ~/tlcl-summer-drills -maxdepth 2 -type f -print
```

---
# Saturday: Processes and Job Control

Focus: Chapter 10.

## 1. Inspect processes

```bash
ps
ps -o pid,ppid,stat,ni,cmd
echo "$$"
```

## 2. Foreground process and Ctrl-c

```bash
sleep 60
```

Stop it with `Ctrl-c`, then verify:

```bash
pgrep -a sleep
```

## 3. Background process

```bash
sleep 120 &
jobs -l
pgrep -a sleep
kill "$!"
```

## 4. Pause, resume, foreground, background

```bash
sleep 180
```

Press `Ctrl-z`, then run:

```bash
jobs -l
bg %1
jobs -l
fg %1
```

Stop it with `Ctrl-c`.

## 5. Signals

```bash
sleep 300 &
PID=$!
echo "$PID"
kill -STOP "$PID"
ps -o pid,stat,cmd -p "$PID"
kill -CONT "$PID"
ps -o pid,stat,cmd -p "$PID"
kill "$PID"
ps -p "$PID"
```

## 6. `top`

```bash
yes > /dev/null &
YES_PID=$!
top
```

Inside `top`, find `yes`, then quit with `q`. Clean up:

```bash
kill "$YES_PID"
pgrep -a yes
```

## 7. `nice` and `renice`

```bash
nice -n 10 sleep 180 &
PID=$!
ps -o pid,ni,cmd -p "$PID"
renice 15 -p "$PID"
ps -o pid,ni,cmd -p "$PID"
kill "$PID"
```

## Cleanup

```bash
killall sleep 2>/dev/null || true
killall yes 2>/dev/null || true
jobs
```
