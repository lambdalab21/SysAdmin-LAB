#Book/The-Linux-Command-Line #Author/Shotts #README  
Most of Author/Shotts Part III should **not** be read as one uninterrupted block. Insert each chapter when Ward or the server project creates a reason to care.

In the 3rd edition, Part III is:

```text
Ch. 14  Package Management
Ch. 15  Storage Media
Ch. 16  Networking
Ch. 17  Searching for Files
Ch. 18  Archiving and Backup
Ch. 19  Regular Expressions
Ch. 20  Text Processing
Ch. 21  Formatting Output
Ch. 22  Printing
Ch. 23  Compiling Programs
```

That chapter list is from No Starch Press’s official table of contents. ([nostarch.com](https://nostarch.com/linux-command-line-3e "The Linux Command Line, 3rd Edition | No Starch Press"))

## Recommended placement

| Author/Shotts chapter                    | When to read it                                                 | Pair with                                      |          Priority |
| --------------------------------- | --------------------------------------------------------------- | ---------------------------------------------- | ----------------: |
| **Ch. 14 — Package Management**   | Now or immediately before installing Nginx and networking tools | Ward Ch. 7 and the AlmaLinux/Ubuntu comparison |              High |
| **Ch. 15 — Storage Media**        | Later, with disks and filesystems                               | Ward Ch. 4                                     |   High, but delay |
| **Ch. 17 — Searching for Files**  | Read soon, before deeper troubleshooting                        | Ward Ch. 7–8 and Nginx deployment              |         Very high |
| **Ch. 18 — Archiving and Backup** | When introducing backups and deployment safety                  | Static-site project, `rsync`, Git workflow     |              High |
| **Ch. 19 — Regular Expressions**  | Just before text processing                                     | Logs, `grep`, Bash scripting                   |              High |
| **Ch. 20 — Text Processing**      | After regex basics, before or during Ward Ch. 11                | Logs, configuration inspection, scripting      |         Very high |
| **Ch. 21 — Formatting Output**    | After basic text processing                                     | Reporting scripts and lab summaries            |            Medium |
| **Ch. 22 — Printing**             | Skip or skim                                                    | No immediate project need                      |               Low |
| **Ch. 23 — Compiling Programs**   | Much later                                                      | Ward Ch. 15–16                                 | Medium, but delay |

The key point:

> Part III contains several essential operational skills, but the chapters belong in different phases.

# Read now: Ch. 14 and Ch. 17

## Author/Shotts Ch. 14 — Package Management

Read this now because he has already used:

```bash
sudo dnf install nginx
sudo dnf install bind-utils
sudo dnf install traceroute
sudo apt install ...
```

He should stop treating those as magic incantations.

Pair this chapter with a comparison exercise.

### On AlmaLinux

```bash
dnf search nginx
dnf info nginx
rpm -q nginx
rpm -ql nginx | less
dnf provides '*/dig'
```

### On Ubuntu

```bash
apt search nginx
apt show nginx
dpkg -l nginx
dpkg -L nginx | less
apt-file search bin/dig
```

If `apt-file` is unavailable, that itself becomes a package-management exercise.

### What he should learn

- package vs program
    
- repository
    
- dependency
    
- install vs remove
    
- package metadata
    
- which files a package installed
    
- why Ubuntu and AlmaLinux use different package managers
    

This directly supports the Nginx project and his two-distribution lab.

---

## Author/Shotts Ch. 17 — Searching for Files

Read this soon. It is more important than it initially looks.

Infrastructure work constantly requires questions such as:

```text
Where is the config file?
Which log contains this error?
Where did the package install this binary?
Which file contains this setting?
Which files changed recently?
```

Practice:

```bash
find /etc -name '*nginx*'
find /var/log -type f
find /var/www -type f
find /etc -type f -mtime -1
locate sshd_config
command -v nginx
rpm -ql nginx | less
grep -R "server_name" /etc/nginx
```

### Nginx scavenger hunt

Ask him to locate:

```text
Nginx executable
systemd unit file
main config file
site config directory
document root
access log
error log
```

Do not provide paths immediately. Make him discover them.

This develops a durable habit:

```text
Do not memorize every path.
Know how to locate the path.
```

# Read during the deployment phase: Ch. 18

## Author/Shotts Ch. 18 — Archiving and Backup

Read this after the first successful `scp` deployment and before relying on `rsync --delete`.

He should understand that deployment and backup are different.

```text
deployment = update a running system
backup     = preserve recoverable copies
```

A mirrored directory created with:

```bash
rsync -av --delete ./dist/ deploy@app01:/var/www/site1/public/
```

is **not** a backup. If a file disappears from the source, `--delete` may remove it from the destination too.

### Practice

Create files:

```bash
mkdir -p ~/archive-lab/site1/assets
printf 'hello\n' > ~/archive-lab/site1/index.html
printf 'logo\n' > ~/archive-lab/site1/assets/logo.png
```

Create an archive:

```bash
cd ~/archive-lab
tar -cvf site1.tar site1
tar -tvf site1.tar
```

Compress it:

```bash
gzip site1.tar
ls -lh site1.tar.gz
```

Extract into a separate directory:

```bash
mkdir restored
tar -xvf site1.tar.gz -C restored
find restored -print
```

Then compare:

```bash
diff -r site1 restored/site1
```

### What he should learn

- archive vs compression
    
- `tar`
    
- `gzip`
    
- preserving directory structure
    
- restoring into a safe test directory
    
- verifying recovery
    

Later, add a backup script:

```bash
#!/usr/bin/env bash
set -euo pipefail

timestamp=$(date +%Y%m%d-%H%M%S)
tar -czf "$HOME/backups/site1-$timestamp.tar.gz" "$HOME/projects/site1"
```

# Read before serious Bash scripting: Ch. 19 and Ch. 20

## Author/Shotts Ch. 19 — Regular Expressions

Read this after networking or alongside log inspection.

He does not need advanced regex wizardry. He needs enough to search logs and text reliably.

Start with:

```bash
grep 'root' /etc/passwd
grep '^root:' /etc/passwd
grep '/bin/bash$' /etc/passwd
grep -E '^(root|deploy):' /etc/passwd
grep -E '403|404|500' /var/log/nginx/*.log
```

He should understand:

```text
^      beginning of line
$      end of line
.      any single character
*      repeat previous pattern
[]     character class
()     grouping with extended regex
|      alternation with extended regex
```

Do not spend days on regex puzzles.

The correct target is:

> Can he write a search pattern that answers a real troubleshooting question?

---

## Author/Shotts Ch. 20 — Text Processing

Read this before or during Ward Ch. 11 shell scripting.

This is one of the most valuable Part III chapters for infrastructure work.

Practice with safe data:

```bash
cut -d: -f1 /etc/passwd
sort /etc/passwd
sort -t: -k3 -n /etc/passwd
uniq
wc -l /etc/passwd
head /etc/passwd
tail /etc/passwd
tr '[:lower:]' '[:upper:]'
```

Use pipes:

```bash
cut -d: -f7 /etc/passwd | sort | uniq -c | sort -nr
```

Inspect Nginx logs:

```bash
awk '{print $9}' /var/log/nginx/access.log | sort | uniq -c | sort -nr
```

That command counts HTTP response codes.

Another useful example:

```bash
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head
```

That shows frequent client IP addresses.

### What he should learn

- text streams
    
- pipelines
    
- filters
    
- composability
    
- why plain-text logs are valuable
    
- when Bash is appropriate
    
- when a Python script would be clearer
    

This is where Unix philosophy becomes concrete.

# Read after text processing: Ch. 21

## Author/Shotts Ch. 21 — Formatting Output

Read selectively after Ch. 20.

This chapter is useful when his scripts begin producing reports. It is not urgent for installing services.

Useful commands:

```bash
printf '%-20s %s\n' "HOST" "STATUS"
printf '%-20s %s\n' "app01" "reachable"
printf '%-20s %s\n' "db01" "reachable"
```

Create a simple report:

```bash
printf '%-12s %-15s %-10s\n' "HOST" "IP" "HTTP"
printf '%-12s %-15s %-10s\n' "app01" "192.168.1.20" "OK"
```

Later, use it in:

```text
check-sites.sh
inventory.sh
backup-report.sh
```

### Priority

Useful, but do not delay the infrastructure project for it.

# Delay Ch. 15 until Ward Ch. 4

## Author/Shotts Ch. 15 — Storage Media

Read this with Ward Ch. 4, not immediately.

The pairing is strong:

```text
Author/Shotts = commands under his fingers
Ward   = filesystems, disks, mounts, and internal model
```

Practice later on an extra virtual disk attached to a disposable VM:

```bash
lsblk
blkid
df -h
du -sh /var/www
mount
findmnt
```

Then, with care:

```bash
sudo fdisk -l
```

When using an extra lab disk only:

```bash
sudo mkfs.ext4 /dev/<lab-device>
sudo mkdir -p /mnt/labdisk
sudo mount /dev/<lab-device> /mnt/labdisk
findmnt /mnt/labdisk
```

Do **not** experiment with formatting commands on a disk containing data.

### Best timing

Read Ch. 15 after:

```text
networking
→ Nginx deployment
→ rsync
→ backups
```

Then use it to understand where deployed files physically live and how storage is attached.

# Delay Ch. 23 until Ward Ch. 15–16

## Author/Shotts Ch. 23 — Compiling Programs

Read this much later.

It belongs with Ward’s development-tool and source-compilation chapters, not with the Nginx deployment phase.

Eventually, he should understand:

```bash
gcc hello.c -o hello
./hello
ldd ./hello
make
```

And the general pattern:

```text
source code
→ compiler
→ object files
→ linker
→ executable
```

This becomes useful before reading selected parts of Kerrisk’s _The Linux Programming Interface_.

But it is not a summer priority unless he is especially curious.

# Skip or skim Ch. 22

## Author/Shotts Ch. 22 — Printing

Skim it if he wants to understand Unix printing pipelines or if he actually administers printers.

Otherwise, skip for now.

This is not because printing is nonexistent. It is because his current project gives much higher returns elsewhere:

```text
networking
permissions
services
logs
deployment
Bash
backups
```

# Integrated reading order

Here is the clean combined path.

## Already completed or underway

```text
Ward 1–2
→ Author/Shotts 1–10 selectively
→ Ward 7–8
```

## Next: prepare for networking

```text
Author/Shotts 14 — Package Management
→ Author/Shotts 17 — Searching for Files
```

These chapters immediately improve his Nginx and troubleshooting work.

## Networking phase

```text
Ward 9
→ Author/Shotts 16
→ selected Lucas chapters
→ Ward 10
→ Evans networking zines
```

## Deployment and operational safety

```text
static Nginx deployment
→ scp
→ rsync
→ Author/Shotts 18 — Archiving and Backup
```

## Automation phase

```text
Author/Shotts 19 — Regular Expressions
→ Author/Shotts 20 — Text Processing
→ Ward 11
→ Author/Shotts Part IV scripting chapters
→ deploy.sh
→ check-site.sh
→ backup-site.sh
```

## Later systems phase

```text
Ward 4
→ Author/Shotts 15 — Storage Media
→ Ward 6
→ backend app managed by systemd
```

## Much later

```text
Ward 15–16
→ Author/Shotts 23 — Compiling Programs
→ selected Kerrisk chapters
→ Ward 17 containers
```

# Which chapters deserve real practice?

Use this priority table.

|Chapter|Read?|Practice deeply?|
|---|--:|--:|
|Ch. 14 Package Management|Yes, now|Yes|
|Ch. 15 Storage Media|Yes, later|Yes, on disposable disk|
|Ch. 17 Searching for Files|Yes, soon|Yes|
|Ch. 18 Archiving and Backup|Yes, during deployment|Yes|
|Ch. 19 Regular Expressions|Yes, before scripting|Moderate|
|Ch. 20 Text Processing|Yes, before scripting|Yes|
|Ch. 21 Formatting Output|Selectively|Light|
|Ch. 22 Printing|Skip or skim|No|
|Ch. 23 Compiling Programs|Later|Moderate|

# The practical summer balance

He will spend a substantial portion of the summer on networking and Bash, but Part III prevents the curriculum from becoming too narrow.

A sensible balance is:

```text
networking and services       30%
shell and text processing     25%
deployment and backup         20%
permissions and processes     15%
package and storage basics    10%
```

The goal is not to finish chapters quickly.

The goal is to build a coherent operational loop:

```text
install package
→ locate files
→ inspect configuration
→ start service
→ inspect process
→ check port
→ deploy files
→ read logs
→ search and filter output
→ back up
→ automate carefully
```

That loop is the real infrastructure curriculum.