#HLW #Book/How-Linux-Works 
# What he should pay attention to in Ward Ch. 7

Ward Ch. 7 is about **system configuration**. For him, the important parts are not trivia. The important question is:

> Where does Linux store decisions about users, permissions, services, logs, scheduled jobs, and system behavior?

He should focus on these.

## 1. Users and groups

He should understand these files:

```bash
/etc/passwd
/etc/shadow
/etc/group
/etc/sudoers
/etc/sudoers.d/
```

He needs to know:

- A user is not just a name; it has a **UID**.
    
- A group is not just a label; it has a **GID**.
    
- `/etc/passwd` does not usually store the actual password.
    
- `/etc/shadow` stores password hashes and aging information.
    
- `sudo` is controlled by policy, not magic.
    
- Being in the `sudo` or `wheel` group matters, depending on distro.
    

Ubuntu usually uses the `sudo` group. AlmaLinux/RHEL-style systems commonly use `wheel`.

Lab commands:

```bash
id
id username
getent passwd username
getent group sudo
getent group wheel
sudo -l
```

He should be able to answer:

> Why can one user run `sudo` but another cannot?

If he cannot answer that, he does not understand the users he created.

---

## 2. Permissions

He should already know basic permissions before going too deep into services.

Important commands:

```bash
ls -l
chmod
chown
chgrp
umask
namei -l /some/path
```

He should understand:

```text
-rw-r--r--
drwxr-xr-x
```

And he must understand that directory permissions are different from file permissions.

For directories:

- `r` = can list names
    
- `w` = can create/delete/rename entries
    
- `x` = can enter/traverse the directory
    

This is one of those things beginners fake understanding of. Test him on it.

---

## 3. Logging

He should learn `journalctl` now.

Important commands:

```bash
journalctl
journalctl -xe
journalctl -u ssh
journalctl -u sshd
journalctl --since today
journalctl --since "1 hour ago"
```

Ubuntu may use service name `ssh`; AlmaLinux often uses `sshd`.

He should compare:

```bash
systemctl status ssh
systemctl status sshd
```

He should understand:

- logs are not just “error messages”
    
- logs are historical evidence
    
- services write to logs
    
- authentication attempts are visible
    
- failed SSH logins should leave traces
    

---

## 4. systemd basics

He does not need full systemd mastery yet, but he needs basic service literacy.

Commands:

```bash
systemctl status ssh
systemctl status sshd
systemctl is-enabled ssh
systemctl is-enabled sshd
systemctl list-units --type=service
systemctl list-unit-files --type=service
```

He should understand the difference between:

```bash
systemctl start service
systemctl stop service
systemctl restart service
systemctl enable service
systemctl disable service
```

This is critical.

- `start` = run now
    
- `enable` = start automatically at boot
    

Many beginners confuse those. Do not let him.

---

## 5. SSH configuration

He already set up SSH, so now he should inspect what changed.

Important files:

```bash
/etc/ssh/sshd_config
~/.ssh/authorized_keys
~/.ssh/config
```

Important commands:

```bash
systemctl status ssh
systemctl status sshd
ss -tulpn | grep :22
sudo sshd -T | less
```

`sshd -T` is especially useful because it shows the effective SSH server configuration.

He should answer:

- Is password login allowed?
    
- Is root login allowed?
    
- Which port is SSH listening on?
    
- Which users can log in?
    
- Where is the public key stored?
    
- What permissions should `~/.ssh` and `authorized_keys` have?
    

Expected permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 6. Firewall configuration

Because he has Ubuntu and AlmaLinux, this is a perfect comparison.

Ubuntu likely:

```bash
sudo ufw status verbose
```

AlmaLinux likely:

```bash
sudo firewall-cmd --state
sudo firewall-cmd --list-all
sudo firewall-cmd --get-active-zones
```

He should understand:

- firewall rules are not the same as services
    
- a service can be running but blocked by firewall
    
- a port can be open locally but unreachable remotely
    
- Ubuntu and AlmaLinux use different firewall frontends
    

Useful check:

```bash
ss -tulpn
```

Then from another machine:

```bash
ssh user@server-ip
nc -vz server-ip 22
```

He should learn this distinction:

> `ss` tells you whether the machine is listening.  
> `nc` from another machine tells you whether the network path allows access.

That distinction is infrastructure thinking.

---

## 7. Scheduled jobs

He should understand both old and new styles:

```bash
crontab -l
systemctl list-timers
```

He does not need deep cron yet, but he should know scheduled jobs exist and can change a machine without anyone actively typing commands.

---

# Labs he should do after Ward Ch. 7

## Lab 1: Reconstruct what he did

Have him write a short report:

```text
On Ubuntu:
- Users I created:
- Groups each user belongs to:
- Which users can sudo:
- SSH service name:
- SSH config file:
- SSH port:
- Firewall tool:
- Firewall allowed services/ports:

On AlmaLinux:
- Users I created:
- Groups each user belongs to:
- Which users can sudo:
- SSH service name:
- SSH config file:
- SSH port:
- Firewall tool:
- Firewall allowed services/ports:
```

This forces him to stop being a command follower.

---

## Lab 2: Create three users with different permissions

Example:

```text
adminuser  = can sudo
devuser    = cannot sudo, can SSH
guestuser  = cannot sudo, cannot SSH if possible
```

Commands to investigate:

```bash
sudo useradd testuser
sudo passwd testuser
sudo usermod -aG sudo testuser
sudo usermod -aG wheel testuser
id testuser
sudo -l -U testuser
```

Then remove sudo access and verify.

Ubuntu:

```bash
sudo gpasswd -d testuser sudo
```

AlmaLinux:

```bash
sudo gpasswd -d testuser wheel
```

He should test, not assume.

---

## Lab 3: Shared project directory

Create a group:

```bash
sudo groupadd project
```

Create a directory:

```bash
sudo mkdir /srv/project
sudo chgrp project /srv/project
sudo chmod 2775 /srv/project
```

Add users:

```bash
sudo usermod -aG project devuser
```

Test:

```bash
touch /srv/project/test.txt
ls -l /srv/project
```

He should learn why `2775` is used. The `2` sets the setgid bit so new files inherit the directory’s group.

This is a real sysadmin skill.

---

## Lab 4: Break SSH safely, then fix it

Only do this with physical access to the machine or another working admin session open.

Steps:

1. Back up config:
    

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

2. Check config before restart:
    

```bash
sudo sshd -t
```

3. Make one harmless change, for example:
    

```text
PasswordAuthentication no
```

4. Restart service:
    

Ubuntu:

```bash
sudo systemctl restart ssh
```

AlmaLinux:

```bash
sudo systemctl restart sshd
```

5. Test login from another machine.
    
6. Read logs:
    

```bash
journalctl -u ssh --since "10 minutes ago"
journalctl -u sshd --since "10 minutes ago"
```

The lesson:

> Never restart a remote access service blindly. Test config first.

---

## Lab 5: Firewall experiment

Start a simple web server:

```bash
python3 -m http.server 8000
```

Check listening port:

```bash
ss -tulpn | grep 8000
```

From another machine:

```bash
curl http://server-ip:8000
```

Then block or allow the port.

Ubuntu:

```bash
sudo ufw deny 8000
sudo ufw allow 8000
sudo ufw status numbered
```

AlmaLinux:

```bash
sudo firewall-cmd --add-port=8000/tcp
sudo firewall-cmd --list-all
sudo firewall-cmd --remove-port=8000/tcp
```

He should observe:

- service running + firewall closed = unreachable
    
- service stopped + firewall open = unreachable
    
- service running + firewall open = reachable
    

This is a core infrastructure concept.

---

## Lab 6: Logs from failed login

From another machine, intentionally fail SSH login.

Then inspect logs:

Ubuntu:

```bash
journalctl -u ssh --since "5 minutes ago"
```

AlmaLinux:

```bash
journalctl -u sshd --since "5 minutes ago"
```

Also try:

```bash
sudo grep ssh /var/log/auth.log
sudo grep sshd /var/log/secure
```

Ubuntu often uses `/var/log/auth.log`; AlmaLinux often uses `/var/log/secure`.

The lesson:

> Authentication leaves evidence.

This connects directly to security.

---

## Lab 7: Compare Ubuntu vs AlmaLinux

Have him make a comparison table.

|Topic|Ubuntu|AlmaLinux|
|---|---|---|
|Package manager|`apt`|`dnf`|
|Admin group|`sudo`|`wheel`|
|SSH service|often `ssh`|often `sshd`|
|Firewall frontend|often `ufw`|often `firewalld`|
|Auth log|`/var/log/auth.log` or journal|`/var/log/secure` or journal|
|SELinux|usually not enforcing by default|often enforcing|

That last one, SELinux, matters later. Do not go deep yet, but he should know AlmaLinux may block things even when normal Unix permissions look correct.

---

# What should he read in LCL now?

Yes, use LCL now. Do not rush into Ward Ch. 8 before cleaning up command-line fundamentals.

Priority LCL chapters now:

## Read now

- **LCL Ch. 6 — Redirection**
    
- **LCL Ch. 7 — Expansion and quoting**
    
- **LCL Ch. 9 — Permissions**
    
- **LCL Ch. 10 — Processes**
    
- **LCL Ch. 11 — Environment**
    

If he has not read those yet, pause Ward after Ch. 7 and do these.

Why? Because Ward Ch. 8 is about processes and resources. LCL Ch. 10 gives him the command-line handling of processes first.

# Should he move to Ward Ch. 8 next?

Yes, but only after he can answer these from Ch. 7:

## Ch. 7 checkpoint

He should be able to explain:

1. What changed when he created a user?
    
2. What file shows user accounts?
    
3. What file stores password hashes?
    
4. What group gives sudo access on Ubuntu?
    
5. What group gives sudo access on AlmaLinux?
    
6. What is the difference between `systemctl start` and `systemctl enable`?
    
7. How does he check whether SSH is running?
    
8. How does he check whether SSH is listening on port 22?
    
9. How does he check whether the firewall allows SSH?
    
10. Where can he see failed SSH login attempts?
    
11. What is the difference between a service running and a port being reachable?
    
12. What is the difference between Unix permissions and firewall rules?
    

If he cannot answer those, he should not move on yet.

# Best next sequence

Do this:

## Step 1: Ward Ch. 7 slowly

Read Ch. 7 while inspecting the actual Ubuntu and Alma machines.

## Step 2: LCL Ch. 6, 7, 9, 10, 11

Especially permissions, quoting, processes, environment.

## Step 3: Ch. 7 labs above

Do at least:

- user/group lab
    
- shared directory lab
    
- SSH inspection lab
    
- firewall port 8000 lab
    
- failed SSH login log lab
    

## Step 4: Ward Ch. 8

Then move to processes and resource utilization.

Ward Ch. 8 will become much more meaningful after he has:

```bash
ps
top
kill
jobs
ss
lsof
journalctl
systemctl
```

under his fingers.

# What his next real project should be

After Ch. 7 and Ch. 8, give him this project:

> Deploy a small web app as a real Linux service.

Minimum requirements:

1. App runs as a non-root user.
    
2. App lives under `/srv/myapp`.
    
3. App starts with `systemd`.
    
4. App logs to `journalctl`.
    
5. Firewall allows only SSH and the app port.
    
6. Admin user can manage it with `sudo`.
    
7. Normal user cannot modify system files.
    
8. He can explain every file and command used.
    

That project ties together users, permissions, services, logs, firewall, processes, and networking.

That is far better than “I read another chapter.”