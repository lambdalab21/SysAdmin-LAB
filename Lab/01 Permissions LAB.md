#LAB #deployment #deployment-document
#permission 
[LAB](https://chatgpt.com/s/t_6a17a20cd6688191ae5146efc6f10e26)

Below is a permission lab he can run on **both machines**.

Assume:

```text
Ubuntu machine:    db01
AlmaLinux machine: app01
Normal user:       john
Admin user:        john-admin
```

Adjust the names as needed.

---

# Permission Lab: Ubuntu + AlmaLinux

## Big difference to remember

|Concept|Ubuntu/Debian|AlmaLinux/RHEL/Rocky|
|---|---|---|
|Admin group|`sudo`|`wheel`|
|Add normal user|`sudo adduser john`|`sudo useradd -m john && sudo passwd john`|
|Add admin user|`sudo usermod -aG sudo john-admin`|`sudo usermod -aG wheel john-admin`|
|SSH service name|usually `ssh`|usually `sshd`|
|Package manager|`apt`|`dnf`|

The file permission model is the same on both.

---

# Lab 1: Identify current user and groups

Run on **both Ubuntu and AlmaLinux**:

```bash
whoami
id
groups
pwd
```

Also run:

```bash
cat /etc/passwd | less
cat /etc/group | less
```

Check admin groups.

On Ubuntu:

```bash
getent group sudo
```

On AlmaLinux:

```bash
getent group wheel
```

Questions he must answer:

```text
What user am I logged in as?
What is my UID?
What is my primary group?
What extra groups am I in?
Is this account an admin account?
How can I tell?
```

Expected lesson:

```text
Admin power usually comes from group membership.
Ubuntu uses sudo.
AlmaLinux uses wheel.
```

---

# Lab 2: Create normal and admin users

## On Ubuntu

Create normal user:

```bash
sudo adduser john
```

Create admin user:

```bash
sudo adduser john-admin
sudo usermod -aG sudo john-admin
```

Check:

```bash
id john
id john-admin
getent group sudo
```

Expected:

```text
john is NOT in sudo.
john-admin IS in sudo.
```

---

## On AlmaLinux

Create normal user:

```bash
sudo useradd -m john
sudo passwd john
```

Create admin user:

```bash
sudo useradd -m john-admin
sudo passwd john-admin
sudo usermod -aG wheel john-admin
```

Check:

```bash
id john
id john-admin
getent group wheel
```

Expected:

```text
john is NOT in wheel.
john-admin IS in wheel.
```

---

## Test both accounts

On both systems:

```bash
su - john
sudo whoami
```

Expected: fail.

Then:

```bash
exit
su - john-admin
sudo whoami
```

Expected:

```text
root
```

Lesson:

```text
john is a normal user.
john-admin can temporarily become root through sudo.
```

---

# Lab 3: Understand `ls -l`

As `john`, run this on both systems:

```bash
mkdir -p ~/perm-lab
cd ~/perm-lab
echo "hello" > file.txt
ls -l
```

Example output:

```text
-rw-r--r-- 1 john john 6 May 27 10:00 file.txt
```

He must explain each part:

```text
-           regular file
rw-         owner can read/write
r--         group can read
r--         others can read
john        owner
john        group
6           size
file.txt    filename
```

Then run:

```bash
stat file.txt
```

Lesson:

```text
ls -l gives the quick view.
stat gives the detailed view.
```

---

# Lab 4: File permission changes with `chmod`

As `john`, on both systems:

```bash
cd ~/perm-lab

chmod 600 file.txt
ls -l file.txt
cat file.txt

chmod 644 file.txt
ls -l file.txt
cat file.txt

chmod 000 file.txt
ls -l file.txt
cat file.txt
```

Expected after `chmod 000`:

```text
Permission denied
```

Restore:

```bash
chmod 644 file.txt
```

Now try symbolic permissions:

```bash
chmod u+x file.txt
ls -l file.txt

chmod g-r file.txt
ls -l file.txt

chmod o-r file.txt
ls -l file.txt
```

Questions:

```text
What does chmod 600 mean?
What does chmod 644 mean?
What does chmod 000 mean?
What does u+x mean?
What does g-r mean?
What does o-r mean?
```

Key:

```text
u = user/owner
g = group
o = others
a = all
r = read
w = write
x = execute
```

---

# Lab 5: Executable script permission

As `john`, on both systems:

```bash
cd ~/perm-lab
echo 'echo hello from script' > hello.sh
ls -l hello.sh
./hello.sh
```

Expected:

```text
Permission denied
```

Now:

```bash
chmod +x hello.sh
ls -l hello.sh
./hello.sh
```

Expected:

```text
hello from script
```

Then inspect:

```bash
file hello.sh
cat hello.sh
```

Lesson:

```text
A file containing commands is not executable until execute permission is granted.
```

Important distinction:

```text
read permission lets you read the script.
execute permission lets you run the script.
```

---

# Lab 6: Directory permissions

This one is important. Beginners often misunderstand it.

As `john`, on both systems:

```bash
cd ~/perm-lab
mkdir testdir
echo "secret" > testdir/secret.txt
ls -ld testdir
ls -l testdir
```

Now remove execute permission from the directory:

```bash
chmod 600 testdir
ls -ld testdir
ls testdir
cat testdir/secret.txt
```

Expected: likely permission problems.

Restore:

```bash
chmod 700 testdir
ls testdir
cat testdir/secret.txt
```

Now try:

```bash
chmod 400 testdir
ls -ld testdir
ls testdir
cat testdir/secret.txt
```

Restore:

```bash
chmod 700 testdir
```

Questions:

```text
What does read mean on a directory?
What does write mean on a directory?
What does execute mean on a directory?
Why is x on a directory different from x on a file?
```

Important answer:

```text
Directory read permission allows listing names.
Directory write permission allows creating/deleting/renaming entries.
Directory execute permission allows entering/traversing the directory.
```

The directory `x` bit is not optional. Without it, the user cannot properly access contents.

---

# Lab 7: Ownership with `chown`

As admin, on both systems:

```bash
sudo mkdir -p /srv/perm-lab
sudo touch /srv/perm-lab/root-owned.txt
ls -l /srv/perm-lab
```

Expected:

```text
root root root-owned.txt
```

As `john`, try:

```bash
echo "hello" > /srv/perm-lab/root-owned.txt
```

Expected:

```text
Permission denied
```

Now as admin:

```bash
sudo chown john:john /srv/perm-lab/root-owned.txt
ls -l /srv/perm-lab/root-owned.txt
```

As `john`, try again:

```bash
echo "hello" > /srv/perm-lab/root-owned.txt
cat /srv/perm-lab/root-owned.txt
```

Lesson:

```text
File ownership matters.
Root can change ownership.
Normal users usually cannot give files away to other users.
```

---

# Lab 8: Group-based shared directory

This lab teaches practical team permissions.

## On Ubuntu

As admin:

```bash
sudo groupadd appteam
sudo usermod -aG appteam john
sudo mkdir -p /srv/appteam
sudo chown root:appteam /srv/appteam
sudo chmod 2770 /srv/appteam
ls -ld /srv/appteam
```

## On AlmaLinux

Same commands:

```bash
sudo groupadd appteam
sudo usermod -aG appteam john
sudo mkdir -p /srv/appteam
sudo chown root:appteam /srv/appteam
sudo chmod 2770 /srv/appteam
ls -ld /srv/appteam
```

Then log out and log back in as `john`.

Check:

```bash
id
```

He should see `appteam`.

Now as `john`:

```bash
cd /srv/appteam
touch john-file.txt
ls -l
```

Expected group should be:

```text
appteam
```

Why? Because `chmod 2770` sets the **setgid bit** on the directory.

Explain:

```text
2    = setgid on directory
770  = owner and group full access, others no access
```

Lesson:

```text
Shared directories usually use groups.
The setgid bit helps new files inherit the directory group.
```

---

# Lab 9: `umask`

As `john`, on both systems:

```bash
umask
touch umask-file.txt
mkdir umask-dir
ls -l umask-file.txt
ls -ld umask-dir
```

Typical result may be:

```text
0022
-rw-r--r-- file
drwxr-xr-x directory
```

Now temporarily change umask:

```bash
umask 077
touch private-file.txt
mkdir private-dir
ls -l private-file.txt
ls -ld private-dir
```

Expected:

```text
-rw------- private-file.txt
drwx------ private-dir
```

Restore for the current shell:

```bash
umask 022
```

Questions:

```text
What does umask control?
Why did new files become private after umask 077?
Does umask change existing files?
```

Answer:

```text
umask affects default permissions for newly created files and directories.
It does not retroactively change old files.
```

---

# Lab 10: `sudo` policy difference

## Ubuntu

Run:

```bash
sudo visudo
```

Look for a line like:

```text
%sudo   ALL=(ALL:ALL) ALL
```

Exit without changing.

Check:

```bash
getent group sudo
```

## AlmaLinux

Run:

```bash
sudo visudo
```

Look for:

```text
%wheel  ALL=(ALL)       ALL
```

Exit without changing.

Check:

```bash
getent group wheel
```

Lesson:

```text
sudo rules are configured.
They are not magic.
Ubuntu commonly grants sudo to the sudo group.
AlmaLinux commonly grants sudo to the wheel group.
```

Warning:

```text
Do not edit /etc/sudoers directly.
Use visudo because it checks syntax.
A broken sudoers file can lock you out of admin access.
```

---

# Lab 11: SSH directory permissions

As `john`, on both systems:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
ls -ld ~/.ssh
ls -l ~/.ssh
```

Expected:

```text
drwx------ .ssh
-rw------- authorized_keys
```

Now deliberately make it bad:

```bash
chmod 777 ~/.ssh
ls -ld ~/.ssh
```

Try SSH key login from another machine. It may fail, depending on SSH settings.

Check logs.

On Ubuntu:

```bash
sudo journalctl -u ssh -n 50
```

On AlmaLinux:

```bash
sudo journalctl -u sshd -n 50
```

Restore:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Lesson:

```text
SSH is strict about permissions because loose permissions can expose login keys.
```

---

# Lab 12: Service account for an app

This is highly relevant to his Clojure app.

## Ubuntu

Create a system user:

```bash
sudo adduser --system --group --home /opt/clojure-crud clojureapp
```

Check:

```bash
id clojureapp
getent passwd clojureapp
ls -ld /opt/clojure-crud
```

## AlmaLinux

Create a system user:

```bash
sudo useradd --system --create-home --home-dir /opt/clojure-crud --shell /sbin/nologin clojureapp
```

Check:

```bash
id clojureapp
getent passwd clojureapp
ls -ld /opt/clojure-crud
```

Then on both systems:

```bash
sudo mkdir -p /opt/clojure-crud/app
sudo chown -R clojureapp:clojureapp /opt/clojure-crud
sudo chmod 750 /opt/clojure-crud
ls -ld /opt/clojure-crud
```

Questions:

```text
Why should the Clojure app not run as root?
Why should the app not run as john?
Why create clojureapp?
What damage is limited if the app is compromised?
```

Correct lesson:

```text
Applications should run as dedicated service accounts with limited privileges.
```

This is one of the most important infrastructure lessons.

---

# Lab 13: Compare `/tmp` sticky bit

On both systems:

```bash
ls -ld /tmp
```

Expected:

```text
drwxrwxrwt
```

Notice the final `t`.

As `john`:

```bash
touch /tmp/john-test.txt
ls -l /tmp/john-test.txt
```

Create another user if needed:

```bash
sudo adduser alice        # Ubuntu
```

On AlmaLinux:

```bash
sudo useradd -m alice
sudo passwd alice
```

As `alice`:

```bash
su - alice
rm /tmp/john-test.txt
```

Expected:

```text
Operation not permitted
```

Lesson:

```text
/tmp is writable by many users, but the sticky bit prevents users from deleting files they do not own.
```

Without the sticky bit, shared writable directories would be dangerous.

---

# Lab 14: Permission troubleshooting drill

Create a broken setup.

As admin:

```bash
sudo mkdir -p /srv/broken
sudo touch /srv/broken/data.txt
sudo chown root:root /srv/broken/data.txt
sudo chmod 600 /srv/broken/data.txt
ls -l /srv/broken/data.txt
```

As `john`:

```bash
cat /srv/broken/data.txt
echo test >> /srv/broken/data.txt
```

Expected: fail.

Now he must diagnose using only:

```bash
whoami
id
ls -ld /srv
ls -ld /srv/broken
ls -l /srv/broken/data.txt
stat /srv/broken/data.txt
```

Then fix it in two possible ways.

### Fix A: make `john` the owner

```bash
sudo chown john:john /srv/broken/data.txt
sudo chmod 600 /srv/broken/data.txt
```

### Fix B: use a group

```bash
sudo groupadd brokenreaders
sudo usermod -aG brokenreaders john
sudo chgrp brokenreaders /srv/broken/data.txt
sudo chmod 640 /srv/broken/data.txt
```

Then log out and back in.

Lesson:

```text
Do not randomly chmod 777.
Diagnose owner, group, and mode.
```

That last sentence matters. `chmod 777` is the beginner’s lazy hammer.

---

# Lab 15: The dangerous `chmod 777` lesson

Create:

```bash
mkdir -p ~/bad-permission-lab
echo "private" > ~/bad-permission-lab/private.txt
chmod 777 ~/bad-permission-lab
chmod 666 ~/bad-permission-lab/private.txt
ls -ld ~/bad-permission-lab
ls -l ~/bad-permission-lab/private.txt
```

Now any local user can write to that file.

Have him answer:

```text
Why is 777 dangerous?
Why is 666 dangerous?
When, if ever, is 777 acceptable?
What should I check before changing permissions?
```

Better checklist:

```text
Who owns it?
What group owns it?
Who actually needs access?
Read only or write too?
Is execute needed?
Is this a file or directory?
Is this a service account problem?
Is this a group membership problem?
```

---

# Final comparison exercise

He should create this table himself.

|Task|Ubuntu command|AlmaLinux command|
|---|---|---|
|Add normal user|`sudo adduser john`|`sudo useradd -m john && sudo passwd john`|
|Add admin group|`sudo usermod -aG sudo john-admin`|`sudo usermod -aG wheel john-admin`|
|Check admin group|`getent group sudo`|`getent group wheel`|
|SSH service logs|`journalctl -u ssh`|`journalctl -u sshd`|
|Package install|`sudo apt install ...`|`sudo dnf install ...`|
|Change file mode|`chmod`|`chmod`|
|Change owner|`chown`|`chown`|
|Check user identity|`id`|`id`|
|Check permissions|`ls -l`, `stat`|`ls -l`, `stat`|

The important observation:

```text
User/group/admin tooling differs slightly.
The permission model is the same.
```

---

# What he should be able to explain afterward

He should be able to answer these without looking them up:

```text
What is the difference between john and john-admin?
Why is sudo group used on Ubuntu?
Why is wheel group used on AlmaLinux?
What does chmod 644 mean?
What does chmod 755 mean?
What does chmod 700 mean?
What does chmod 600 mean?
Why does a directory need x permission?
What does chown do?
What does chgrp do?
What does umask do?
What is the sticky bit on /tmp?
What is the setgid bit on a shared directory?
Why should an app run as clojureapp instead of root?
Why is chmod 777 usually wrong?
```

If he can explain those clearly, he is no longer just “typing Linux commands.” He is starting to think like an infrastructure person.