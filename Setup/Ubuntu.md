#linux #setup  #ubuntu

Next thing: **turn the Ubuntu laptop into a clean database server**, not a random Linux playground.

Assume this machine will be:

```
db01 = Ubuntu 26.04 database server
```

## 1. Update the system

```
sudo apt updatesudo apt full-upgrade -ysudo reboot
```

After reboot:

```
lsb_release -auname -a
```

He should write down:

```
Hostname:IP address:Ubuntu version:Kernel version:
```

This is basic professional habit.

---

## 2. Set the hostname

```
sudo hostnamectl set-hostname db01
```

Check:

```
hostnamectl
```

Optional but useful:

```
sudoedit /etc/hosts
```

Make sure it has something like:

```
127.0.1.1 db01
```

---

## 3. Give it a stable IP address

He needs a stable address. Do this either from the router’s DHCP reservation page or directly on Ubuntu.

Simpler: **use router DHCP reservation**.

Example:

```
db01 = 192.168.1.51
```

Then check:

```
ip addrip route
```

Also install useful tools:

```
sudo apt install -y net-tools curl wget vim git htop tree unzip ncdu
```

---

## 4. Set up SSH correctly

Install SSH server:

```
sudo apt install -y openssh-serversudo systemctl enable sshsudo systemctl start sshsudo systemctl status ssh
```

From another machine:

```
ssh username@db01
```

or:

```
ssh username@192.168.1.51
```

Then set up SSH key login from his main laptop:

```
ssh-keygen -t ed25519ssh-copy-id username@192.168.1.51
```

After key login works, harden SSH:

```
sudoedit /etc/ssh/sshd_config
```

Set:

```
PermitRootLogin noPasswordAuthentication no
```

Restart:

```
sudo systemctl restart ssh
```

Important: keep one SSH session open while testing a new one. Do not lock yourself out like an amateur.

---

## 5. Enable firewall

Install and enable `ufw`:

```
sudo apt install -y ufw
```

Allow SSH from the home network:

```
sudo ufw allow from 192.168.1.0/24 to any port 22 proto tcp
```

Enable firewall:

```
sudo ufw enablesudo ufw status verbose
```

At this point, only SSH should be allowed.

---

## 6. Install MariaDB or MySQL

For learning, I recommend **MariaDB** first because it is simple on Debian/Ubuntu systems:

```
sudo apt install -y mariadb-server mariadb-clientsudo systemctl enable mariadbsudo systemctl start mariadbsudo systemctl status mariadb
```

Then run:

```
sudo mariadb-secure-installation
```

Check that it is listening:

```
ss -tulpen | grep mysql
```

At first, it should usually listen only locally:

```
127.0.0.1:3306
```

That is good. Do not expose it yet.

---

## 7. Create the app database and user

Log in:

```
sudo mariadb
```

Then:

```
CREATE DATABASE crudapp CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;CREATE USER 'crud_user'@'192.168.1.50' IDENTIFIED BY 'use-a-real-password-here';GRANT SELECT, INSERT, UPDATE, DELETEON crudapp.*TO 'crud_user'@'192.168.1.50';FLUSH PRIVILEGES;
```

Here I am assuming:

```
app01 = 192.168.1.50db01  = 192.168.1.51
```

Do not use root from the app. Do not grant `ALL PRIVILEGES` unless he has a reason.

---

## 8. Allow remote database access only from the app server

Edit MariaDB config:

```
sudoedit /etc/mysql/mariadb.conf.d/50-server.cnf
```

Find:

```
bind-address = 127.0.0.1
```

Change to:

```
bind-address = 192.168.1.51
```

Restart:

```
sudo systemctl restart mariadb
```

Open firewall only to the app server:

```
sudo ufw allow from 192.168.1.50 to any port 3306 proto tcpsudo ufw status verbose
```

Test from the app server later:

```
nc -vz 192.168.1.51 3306
```

Expected:

```
succeeded
```

Test from some other machine:

```
nc -vz 192.168.1.51 3306
```

Expected:

```
blocked or timed out
```

This is the lesson: **database reachable by the app, not by the whole LAN.**

---

## 9. Install basic monitoring tools

```
sudo apt install -y htop iotop iftop nload tcpdump nmap traceroute dnsutils lsof
```

Commands he should practice:

```
htopdf -hfree -huptimess -tulpensudo lsof -i -P -nsudo tcpdump -i any port 3306journalctl -u mariadb -f
```

He should learn what “normal” looks like before anything breaks.

---

## 10. Create a backup directory

```
sudo mkdir -p /backups/mariadbsudo chmod 700 /backups/mariadb
```

Manual backup:

```
sudo mariadb-dump crudapp > /backups/mariadb/crudapp-$(date +%F).sql
```

Check:

```
ls -lh /backups/mariadb
```

Later he should automate this with cron or a systemd timer.

The rule:

> No backup means no database. It is just temporary data.

---

## 11. Write a server log

Make him keep a plain text file:

```
mkdir -p ~/lab-notesvim ~/lab-notes/db01.md
```

Minimum contents:

```
# db01OS: Ubuntu 26.04Role: database serverIP: 192.168.1.51DB: MariaDBAllowed inbound ports:- 22 from LAN- 3306 from app01 onlyImportant commands:- systemctl status mariadb- journalctl -u mariadb -f- ss -tulpen- ufw status verboseBackup location:- /backups/mariadb
```

This may sound boring. It is not. Documentation separates someone who “played with Linux” from someone who operated a system.

---

# What he should not do yet

Do **not** install ten random tools.

Do **not** install a desktop environment if this is server practice.

Do **not** expose MySQL to the internet.

Do **not** use root database login from the app.

Do **not** start with Docker yet. Let him first understand services, ports, logs, users, permissions, and firewalls.

---

