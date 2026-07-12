#Book/The-Linux-Command-Line #Author/Shotts 
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
# Sunday: Weekly Mixed Review

Focus: Chapters 1–10 together. This checks whether skills transfer across chapters.

## 1. Start from zero

```bash
cd ~
pwd
cd ~/tlcl-summer-drills
pwd
ls -la
```

Say aloud: know where you are before running commands.

## 2. Find and inspect files

```bash
find . -maxdepth 2 -type f -print
file alpha/fruits.txt
cat alpha/fruits.txt
less logs/app.log
```

Inside `less`: `/error`, `n`, `q`.

## 3. Pipeline review

```bash
cat alpha/fruits.txt | sort | uniq
cat alpha/fruits.txt | sort | uniq | wc -l
cat logs/app.log | grep error
cat logs/app.log | grep error | wc -l
```

## 4. Expansion review

```bash
echo *
echo alpha/*.txt
echo {Mon,Tue,Wed,Thu,Fri}
echo file-{1..7}.txt
echo "$HOME"
echo '$HOME'
```

## 5. Command knowledge review

```bash
type cd
type ls
type echo
which bash
help cd | head
man ls
```

Exit `man` with `q`.

## 6. Permission review

```bash
touch sunday-perm.txt
chmod 600 sunday-perm.txt
ls -l sunday-perm.txt
chmod 644 sunday-perm.txt
ls -l sunday-perm.txt
chmod u+x sunday-perm.txt
ls -l sunday-perm.txt
chmod u=rw,g=r,o= sunday-perm.txt
ls -l sunday-perm.txt
```

## 7. Process review

```bash
sleep 120 &
PID=$!
jobs -l
ps -o pid,ppid,stat,cmd -p "$PID"
kill -STOP "$PID"
ps -o pid,stat,cmd -p "$PID"
kill -CONT "$PID"
kill "$PID"
ps -p "$PID"
```

## 8. Keyboard shortcut review

Type but do not execute:

```bash
echo alpha beta gamma delta epsilon
```

Practice: `Ctrl-a`, `Ctrl-e`, `Alt-b`, `Alt-f`, `Ctrl-k`, `Ctrl-y`, `Ctrl-u`, `Ctrl-c`.

Then press `Ctrl-r`, search for `chmod`, and cancel with `Ctrl-g`.

## 9. Weekly self-test

Answer without notes:

1. What command shows the current directory?
2. What command changes to the home directory?
3. What command shows hidden files?
4. What does `>` do?
5. What does `>>` do?
6. What does `2>` redirect?
7. What does `|` connect?
8. What does `chmod 644 file` mean?
9. What does `Ctrl-c` do?
10. What does `jobs -l` show?

Expected answers:

```text
pwd
cd ~ or cd
ls -a
overwrite stdout
append stdout
stderr
stdout of left command to stdin of right command
rw-r--r--
interrupt foreground process
shell jobs with PIDs
```

## Cleanup

```bash
cd ~/tlcl-summer-drills
rm -f sunday-perm.txt
killall sleep 2>/dev/null || true
jobs
```
