#Author/Ward #Book/How-Linux-Works 

After Chapter 1, the goal should be proving he can inspect a running Linux system and explain what is happening.

Assume he is using the Ubuntu laptop as `db01`.

## Lab 1: “What is this machine?”

Have him collect a basic system inventory.

```bash
hostnamectl
uname -a
lsb_release -a
uptime
whoami
id
pwd
```

Then write answers:

```text
Hostname:
OS:
Kernel version:
Architecture:
Current user:
User ID:
Groups:
Uptime:
```

Lesson: Linux is not “Ubuntu.” Ubuntu is the distribution. Linux is the kernel. The running system has users, processes, filesystems, services, and devices.

---

## Lab 2: Explore the root filesystem

Have him run:

```bash
cd /
ls
```

Then inspect:

```bash
ls /bin
ls /sbin
ls /etc
ls /var
ls /home
ls /usr
ls /tmp
ls /proc
ls /dev
```

Assignment: write one sentence for each:

```text
/bin:
/etc:
/var:
/home:
/usr:
/tmp:
/proc:
/dev:
```

Then compare:

```bash
ls -l /bin
ls -l /usr/bin
```

On modern Ubuntu, `/bin` may be a symlink to `/usr/bin`.

Lesson: Linux has a filesystem hierarchy. Programs, config, logs, users, devices, and runtime information live in different places.

---

## Lab 3: `/proc` is not normal files

Run:

```bash
cat /proc/cpuinfo | less
cat /proc/meminfo | less
cat /proc/uptime
cat /proc/version
```

Then:

```bash
ls -l /proc
```

Have him answer:

```text
Is /proc stored on the disk like a normal folder?
What does /proc show?
Why does /proc change while the system is running?
```

Lesson: some “files” are interfaces to the kernel, not ordinary saved documents.

---

## Lab 4: Find devices

Run:

```bash
ls /dev | less
lsblk
blkid
df -h
mount
```

Then plug in a USB drive and run again:

```bash
lsblk
df -h
```

Assignment:

```text
Which disk is the internal drive?
Which partition is mounted as /?
What changes after plugging in the USB drive?
```

Lesson: Linux exposes hardware and storage through device files and mount points.

---

## Lab 5: Process inspection

Run:

```bash
ps aux | less
top
htop
```

Then:

```bash
ps aux | grep ssh
pgrep ssh
```

Start a simple process:

```bash
sleep 300
```

Open another terminal:

```bash
ps aux | grep sleep
kill <PID>
```

Or:

```bash
pkill sleep
```

Assignment:

```text
What is a PID?
What does kill actually do?
What is the difference between a program and a process?
```

Lesson: Linux is a running set of processes managed by the kernel.

---

## Lab 6: Parent and child processes

Run:

```bash
ps -ef
pstree -p
```

Install if needed:

```bash
sudo apt install -y psmisc
```

Then open a shell and run:

```bash
bash
sleep 100
```

In another terminal:

```bash
pstree -p
```

Find:

```text
terminal → shell → sleep
```

Lesson: processes have parents. Commands launched from a shell become child processes.

---

## Lab 7: Exit codes

Run:

```bash
ls /etc
echo $?

ls /does-not-exist
echo $?
```

Then:

```bash
grep root /etc/passwd
echo $?

grep thiswillnotmatch /etc/passwd
echo $?
```

Assignment:

```text
What does exit code 0 mean?
What does a nonzero exit code mean?
Why do scripts care about exit codes?
```

Lesson: Linux commands communicate success/failure through exit codes. This is basic automation knowledge.

---

## Lab 8: Standard input, output, and error

Run:

```bash
echo hello
echo hello > hello.txt
cat hello.txt
```

Then:

```bash
ls /etc > files.txt
cat files.txt
```

Now errors:

```bash
ls /does-not-exist > out.txt
cat out.txt
```

Then:

```bash
ls /does-not-exist 2> error.txt
cat error.txt
```

Then combine:

```bash
ls /etc /does-not-exist > out.txt 2> error.txt
```

Assignment:

```text
What is stdout?
What is stderr?
Why are they separate?
```

Lesson: redirection is one of the core powers of Unix/Linux.

---

## Lab 9: Pipes

Run:

```bash
ps aux | less
ps aux | grep ssh
cat /etc/passwd | less
cat /etc/passwd | grep root
```

Then:

```bash
ls /usr/bin | wc -l
ps aux | wc -l
```

Assignment:

```text
What does | do?
What does wc -l count?
Why are small commands useful together?
```

Lesson: Linux tools are designed to be chained.

---

## Lab 10: Users and permissions

Create a test file:

```bash
mkdir ~/permission-lab
cd ~/permission-lab
echo "secret" > secret.txt
ls -l
```

Change permissions:

```bash
chmod 600 secret.txt
ls -l
chmod 644 secret.txt
ls -l
chmod 000 secret.txt
ls -l
cat secret.txt
chmod 644 secret.txt
```

Create a script:

```bash
echo 'echo hello' > hello.sh
./hello.sh
chmod +x hello.sh
./hello.sh
```

Assignment:

```text
What do r, w, and x mean for a file?
Why did ./hello.sh fail before chmod +x?
What does chmod 644 mean?
What does chmod 600 mean?
```

Lesson: permissions are not decoration. They control execution and access.

---

## Lab 11: Root vs normal user

Run:

```bash
whoami
touch /root/test.txt
```

Expected: permission denied.

Then:

```bash
sudo touch /root/test.txt
sudo ls -l /root/test.txt
```

Assignment:

```text
Why did the first command fail?
What does sudo do?
Why is logging in as root dangerous?
```

Lesson: normal user vs administrator matters.

---

## Lab 12: Config files in `/etc`

Inspect safe files:

```bash
cat /etc/os-release
cat /etc/hostname
cat /etc/hosts
cat /etc/passwd | less
cat /etc/group | less
```

Assignment:

```text
Where is the hostname stored?
Where are user accounts listed?
Where are groups listed?
What is /etc/hosts used for?
```

Important: do not edit randomly. Inspect first.

Lesson: `/etc` is where system configuration usually lives.

---

## Lab 13: Logs

Run:

```bash
journalctl -n 50
journalctl -p warning -n 50
journalctl -u ssh -n 50
```

Try a failed SSH login from another machine, then check:

```bash
journalctl -u ssh -n 100
```

Assignment:

```text
Where did the failed login appear?
What service logged it?
What information was useful?
```

Lesson: real troubleshooting begins with logs.

---

## Lab 14: Services with systemd

Run:

```bash
systemctl status ssh
systemctl list-units --type=service --state=running
```

Then:

```bash
sudo systemctl stop ssh
```

Careful: do this only while physically at the laptop, not only over SSH.

Then:

```bash
sudo systemctl start ssh
sudo systemctl status ssh
```

Assignment:

```text
What is a service?
What does systemctl start/stop/status do?
Why would stopping ssh remotely be risky?
```

Lesson: services are long-running background programs.

---

## Lab 15: Networking basics

Run:

```bash
ip addr
ip route
resolvectl status
ping 8.8.8.8
ping google.com
```

Assignment:

```text
What is this machine's IP address?
What is the default gateway?
What DNS server is being used?
What is the difference between pinging 8.8.8.8 and google.com?
```

Lesson: IP connectivity and DNS are different problems.

---

# Best mini-project after these labs

Have him create a file:

```bash
mkdir -p ~/linux-labs
vim ~/linux-labs/system-report.sh
```

Script:

```bash
#!/usr/bin/env bash

echo "=== Host ==="
hostnamectl

echo
echo "=== Kernel ==="
uname -a

echo
echo "=== Uptime ==="
uptime

echo
echo "=== Disk ==="
df -h

echo
echo "=== Memory ==="
free -h

echo
echo "=== IP Addresses ==="
ip addr

echo
echo "=== Listening Ports ==="
ss -tulpen

echo
echo "=== Running Services ==="
systemctl list-units --type=service --state=running
```

Make it executable:

```bash
chmod +x ~/linux-labs/system-report.sh
```

Run:

```bash
~/linux-labs/system-report.sh > ~/linux-labs/report.txt
```

Then have him explain the output.

That is the key: **do not let him just run commands. Make him explain what each command proves.**

## What I would expect him to know after Chapter 1 labs

He should be able to explain:

```text
Linux kernel vs distribution
process vs program
root user vs normal user
filesystem hierarchy
/proc and /dev
stdout/stderr/stdin
pipes
exit codes
permissions
services
logs
IP address, gateway, DNS
```

If he can explain those clearly, he is building real foundation. If he just copies commands, he is wasting time.