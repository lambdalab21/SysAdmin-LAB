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
# Monday: Shell, Navigation, and System Exploration

Focus: Chapters 1–3.

## 1. Identify the session

```bash
whoami
hostname
pwd
echo "$SHELL"
date
```

Say aloud: “I am USER on HOST. My current directory is DIRECTORY. My shell is SHELL.”

## 2. Navigation without guessing

```bash
cd ~
pwd
cd ~/tlcl-summer-drills
pwd
cd alpha
pwd
cd ..
pwd
cd -
pwd
```

Questions: What does `~` mean? What does `..` mean? What does `cd -` do?

## 3. Absolute versus relative paths

```bash
cd ~/tlcl-summer-drills
pwd
ls alpha
ls ./alpha
ls ~/tlcl-summer-drills/alpha
```

Say aloud: `alpha` and `./alpha` are relative paths. `~/tlcl-summer-drills/alpha` starts from the home directory.

## 4. Explore with `ls`

```bash
ls
ls -l
ls -la
ls -lh
ls -lt
```

Questions: Which option shows hidden files? Which option uses long format? Which option makes sizes readable? Which option sorts by time?

## 5. Inspect content

```bash
file alpha/fruits.txt
cat alpha/fruits.txt
less logs/app.log
```

Inside `less`: `/error`, `n`, `q`.

## 6. Recognize system directories

```bash
ls /
ls /bin | head
ls /etc | head
ls /usr | head
ls /var | head
```

Say aloud: `/bin` and `/usr/bin` contain commands; `/etc` contains configuration; `/var` contains variable data.

## 7. Two-minute review

Run commands that answer: Who am I? Where am I? What files are here? What kind of file is `alpha/fruits.txt`? What is inside `logs/app.log`?
