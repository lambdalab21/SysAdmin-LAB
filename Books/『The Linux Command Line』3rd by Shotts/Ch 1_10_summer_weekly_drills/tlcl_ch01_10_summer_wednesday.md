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
# Wednesday: Commands, Help, and Keyboard Efficiency

Focus: Chapters 5 and 8.

## 1. Command type recognition

```bash
type cd
type ls
type cp
type echo
type grep
type pwd
```

For each, say whether it is a builtin, alias, function, keyword, or executable.

## 2. Locate commands

```bash
which ls
which grep
which bash
type ls
type grep
type bash
```

Say aloud: `which` finds executables in `PATH`; `type` explains how the shell interprets a command.

## 3. Help sources

```bash
help cd | head
man ls
```

Inside `man`: `/--all`, `n`, `q`. Then:

```bash
ls --help | head
```

## 4. Command history

```bash
history | tail
```

Then press `Ctrl-r`, search for `ls`, and cancel with `Ctrl-g`.

## 5. Cursor movement

Type but do not execute:

```bash
echo alpha beta gamma delta epsilon
```

Practice: `Ctrl-a`, `Ctrl-e`, `Alt-b`, `Alt-f`, `Ctrl-c`. Repeat three times.

## 6. Deleting and restoring text

Type but do not execute:

```bash
echo keep-this remove-this-and-this
```

Practice: `Alt-b`, `Alt-b`, `Ctrl-k`, `Ctrl-y`, `Ctrl-c`.

Then type:

```bash
echo remove-left keep-right
```

Move to `keep-right`, press `Ctrl-u`, then `Ctrl-c`.

## 7. Tab completion

```bash
cd ~/tlcl-summer-drills
ls a<Tab>
ls b<Tab>
ls l<Tab>
cd ~/tlcl-summer-drills/a<Tab>
```

Say aloud: use Tab completion instead of typing long paths manually.
