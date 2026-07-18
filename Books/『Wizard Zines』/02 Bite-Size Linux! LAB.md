#Book/Wizard-Zine 
#linux 
#Book/Wizard-Zine 
#linux 
/He should do a **Linux scavenger hunt** and create a one-page “map of the machine.”

The box-set description presents the zines as a way to understand what programs are doing, how Linux system parts communicate, and how to use tools already available on the machine. ([nostarch.com](https://nostarch.com/linuxtoolbox))

Make him inspect the Ubuntu and AlmaLinux machines.

# Goal

He should start viewing Linux as an inspectable system:

```text
files
→ users
→ permissions
→ processes
→ /proc
→ services
→ logs
→ network interfaces
```

The task is not to memorize command output. It is to answer:

> “Where can I look when I want to understand what the machine is doing?”

# One-hour follow-up lab

Use `app01` first. Repeat selected parts later on Ubuntu and compare.

## Part 1: Identify the machine

Run:

```bash
hostname
hostnamectl
uname -a
cat /etc/os-release
```

Write down:

```text
Hostname:
Distribution:
Version:
Kernel version:
Architecture:
```

Questions:

1. What is the difference between the Linux kernel and the Linux distribution?
    
2. Why can Ubuntu and AlmaLinux run different tools while both use Linux?
    
3. What does the hostname identify?
    

---

## Part 2: Explore the filesystem hierarchy

Run:

```bash
ls /
```

Then inspect:

```bash
ls /etc
ls /var
ls /home
ls /usr
ls /tmp
ls /dev
ls /proc
```

Create a short map:

|Directory|What he should associate with it|
|---|---|
|`/etc`|System configuration|
|`/var`|Changing data, including logs|
|`/home`|Normal user files|
|`/usr`|Installed user-space programs and shared data|
|`/tmp`|Temporary files|
|`/dev`|Device files|
|`/proc`|Kernel-exposed process and system information|

Ask him to locate:

```bash
cat /etc/hostname
cat /etc/os-release
ls /var/log
cat /etc/passwd | head
```

Question:

> Which paths contain files stored on disk, and which paths expose kernel-managed information?

He does not need a perfect answer yet. He should notice that `/proc` and `/dev` are unusual.

---

## Part 3: Inspect his own shell as a process

Run:

```bash
echo $$
ps -p $$ -o pid,ppid,user,stat,comm,args
```

Then:

```bash
ls "/proc/$$"
cat "/proc/$$/status" | head -n 20
```

Inspect open file descriptors:

```bash
ls -l "/proc/$$/fd"
```

Inspect the executable:

```bash
readlink "/proc/$$/exe"
```

Questions:

1. What does `$$` represent?
    
2. Why does the shell have a PID?
    
3. What does `/proc/<PID>/status` reveal?
    
4. Why does `/proc/<PID>/fd` contain numbered entries?
    
5. What program is actually running the shell?
    

Julia Evans’s public `/proc` comic emphasizes that every process has a PID and that `/proc/<PID>/status`, `/proc/<PID>/fd`, and related entries expose useful information about a running process. ([wizardzines.com](https://wizardzines.com/comics/))

---

## Part 4: Create a process and watch it appear

Start:

```bash
sleep 300 &
SLEEP_PID=$!
```

Inspect:

```bash
echo "$SLEEP_PID"
ps -p "$SLEEP_PID" -o pid,ppid,user,stat,comm,args
cat "/proc/$SLEEP_PID/status" | head -n 15
```

Terminate:

```bash
kill "$SLEEP_PID"
```

Verify:

```bash
ps -p "$SLEEP_PID"
```

Questions:

1. What did `&` do?
    
2. What does `$!` contain?
    
3. Why did `/proc/<PID>` disappear after the process ended?
    
4. Why was `sleep` normally in a sleeping state?
    

This reinforces Ward Ch. 8 without turning it into a full performance lab.

---

## Part 5: Users, groups, and permissions

Run:

```bash
whoami
id
groups
ls -ld ~
ls -l ~
```

Create a file:

```bash
mkdir -p ~/bite-size-linux-lab
cd ~/bite-size-linux-lab

touch test.txt
ls -l test.txt
chmod 600 test.txt
ls -l test.txt
chmod 644 test.txt
ls -l test.txt
```

Questions:

1. Who owns the file?
    
2. Which group owns it?
    
3. What changed between `600` and `644`?
    
4. Who can read the file?
    
5. Who can modify it?
    

Then inspect a directory:

```bash
mkdir testdir
chmod 700 testdir
ls -ld testdir
chmod 755 testdir
ls -ld testdir
```

Ask:

> Why does execute permission mean something different on a directory than on an ordinary file?

This prepares him for the earlier `scp` permission problem.

---

## Part 6: Discover commands rather than memorizing them

Run:

```bash
type cd
type ls
type grep
command -v python3
command -v ssh
command -v scp
```

Then:

```bash
man ls
```

Inside `man`:

```text
/hidden
n
q
```

Questions:

1. Why is `cd` different from `ls`?
    
2. What is a shell builtin?
    
3. What does `command -v` reveal?
    
4. How do you search inside a man page?
    

The habit should be:

```text
I do not remember the flag
→ inspect documentation
→ test safely
```

not:

```text
guess
→ paste
→ hope
```

---

## Part 7: Inspect a service

Use SSH because he has already configured it.

On AlmaLinux:

```bash
systemctl status sshd
systemctl is-enabled sshd
journalctl -u sshd --since "30 minutes ago"
```

On Ubuntu, the service name may be:

```bash
systemctl status ssh
```

Questions:

1. Is SSH running now?
    
2. Will it start automatically after reboot?
    
3. Where can he inspect recent service activity?
    
4. What is the difference between a service and an ordinary interactive command?
    

---

## Part 8: Compare Ubuntu and AlmaLinux

Repeat selected commands on both machines:

```bash
cat /etc/os-release
uname -r
id
systemctl status ssh
systemctl status sshd
command -v apt
command -v dnf
```

Create a comparison table:

|Topic|Ubuntu|AlmaLinux|
|---|---|---|
|Package manager|`apt`|`dnf`|
|SSH service name|often `ssh`|often `sshd`|
|Admin group|commonly `sudo`|commonly `wheel`|
|Distribution info|`/etc/os-release`|`/etc/os-release`|
|Kernel interface|Linux|Linux|

Question:

> Which differences come from the distribution, and which behaviors come from the Linux kernel?

That distinction is worth building early.

# Create a one-page Linux map

Have him write this from memory afterward:

```text
Machine identity:
  hostname
  /etc/os-release
  uname

Configuration:
  /etc

Logs and changing data:
  /var
  /var/log

Users:
  /etc/passwd
  /etc/group
  id
  groups

Processes:
  ps
  /proc/<PID>
  /proc/<PID>/status
  /proc/<PID>/fd

Services:
  systemctl
  journalctl

Documentation:
  man
  type
  command -v
```

Do not grade the formatting. Grade whether he can explain where he would look.

# Weekly five-minute retention drill

Ask him to answer without notes:

1. Where do system configuration files usually live?
    
2. Where do logs usually live?
    
3. What is `/proc`?
    
4. What does PID mean?
    
5. How can you inspect your current shell’s PID?
    
6. How can you find which executable is running?
    
7. What is the difference between a shell builtin and an external command?
    
8. How do you check whether a service is running?
    
9. How do you inspect a service’s logs?
    
10. What is the difference between Ubuntu and AlmaLinux: kernel or distribution?
    

# Best follow-up sequence

Use:

```text
Bite-Size Linux!
→ Linux scavenger hunt
→ Ward Ch. 8 drills
→ Linux Debugging Tools You’ll Love
→ Spying on Your Programs with strace
→ Ward Ch. 9 networking
```

The zine has done its job when he starts asking:

> “Where can I inspect that?”

instead of:

> “What command should I paste?”