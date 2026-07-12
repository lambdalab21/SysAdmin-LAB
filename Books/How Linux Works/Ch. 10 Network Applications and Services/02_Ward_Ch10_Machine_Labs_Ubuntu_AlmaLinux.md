#Author/Ward #Book/How-Linux-Works 
#network 
# Ward Ch. 10 Machine Labs — Ubuntu + AlmaLinux

These labs are for the two-laptop home lab:

```text
app01 = AlmaLinux/Rocky app server
 db01 = Ubuntu/Debian database server
```

Goal:

> Learn how network services are started, observed, tested, secured, and diagnosed.

Run commands carefully. Do not expose these machines directly to the internet.

---

## Lab 0: Install diagnostic tools

### Ubuntu/db01

```bash
sudo apt update
sudo apt install -y openssh-server lsof tcpdump netcat-openbsd nmap traceroute dnsutils curl
```

### AlmaLinux/app01

```bash
sudo dnf install -y openssh-server lsof tcpdump nmap traceroute bind-utils curl nc
```

Check SSH service names:

Ubuntu:

```bash
systemctl status ssh
```

AlmaLinux:

```bash
systemctl status sshd
```

---

## Lab 1: Prove SSH is a service

### On server machine

Ubuntu:

```bash
systemctl status ssh
ss -tulpen | grep ':22'
sudo lsof -iTCP:22 -sTCP:LISTEN -P -n
```

AlmaLinux:

```bash
systemctl status sshd
ss -tulpen | grep ':22'
sudo lsof -iTCP:22 -sTCP:LISTEN -P -n
```

### From the other machine

```bash
ssh john@app01
ssh john@db01
```

### Questions

```text
1. Which process is listening on port 22?
2. Which user owns the listening process?
3. What does systemd know about it?
4. What does ss show that systemctl does not?
5. What does lsof show that ss does not make obvious?
```

---

## Lab 2: Stop SSH and observe failure

Do this only when you have physical access to the machine. Do not lock yourself out remotely.

### Ubuntu

```bash
sudo systemctl stop ssh
systemctl status ssh
ss -tulpen | grep ':22'
```

From the other machine:

```bash
ssh john@db01
```

Restart:

```bash
sudo systemctl start ssh
```

### AlmaLinux

```bash
sudo systemctl stop sshd
systemctl status sshd
ss -tulpen | grep ':22'
```

From the other machine:

```bash
ssh john@app01
```

Restart:

```bash
sudo systemctl start sshd
```

### Questions

```text
1. What error did the SSH client show?
2. Did port 22 disappear from ss output?
3. What changed in journalctl?
4. Why is stopping SSH remotely dangerous?
```

---

## Lab 3: Client error vs server evidence

From one machine, intentionally connect to a wrong username:

```bash
ssh fakeuser@app01
ssh fakeuser@db01
```

Check logs.

Ubuntu:

```bash
sudo journalctl -u ssh -n 50
```

AlmaLinux:

```bash
sudo journalctl -u sshd -n 50
```

### Write a proper troubleshooting note

```text
Time:
Client machine:
Server machine:
Command tried:
Client error:
Server log evidence:
Conclusion:
```

Bad conclusion:

```text
SSH is broken.
```

Good conclusion:

```text
The server is reachable, but authentication failed for username fakeuser.
```

---

## Lab 4: Port reachability with nc

From app01:

```bash
nc -vz db01 22
nc -vz db01 3306
```

From db01:

```bash
nc -vz app01 22
nc -vz app01 80
```

### Interpret results

```text
succeeded       = TCP connection to that port was possible
connection refused = host reachable, but no process accepted the port
timed out       = firewall, routing, host down, or packet filtering likely
```

### Questions

```text
1. Does nc prove login credentials are correct?
2. Does nc prove the application protocol is working?
3. What does nc actually prove?
```

---

## Lab 5: Watch packets with tcpdump

On db01, run:

```bash
sudo tcpdump -i any port 22
```

From app01, connect:

```bash
ssh john@db01
```

Stop tcpdump with `Ctrl-C`.

Now test database traffic later when MariaDB is running:

```bash
sudo tcpdump -i any port 3306
```

From app01:

```bash
nc -vz db01 3306
```

### Questions

```text
1. Did tcpdump show packets when the client tried to connect?
2. If the client times out and tcpdump shows nothing, where might the problem be?
3. If tcpdump shows packets but the service still fails, what should you inspect next?
```

---

## Lab 6: Scan your own servers with nmap

Only scan your own machines.

```bash
nmap app01
nmap db01
```

Record:

```text
app01 open ports:
db01 open ports:
```

Now ask:

```text
1. Which open ports are expected?
2. Which open ports are surprising?
3. Should db01 expose MySQL/MariaDB to all LAN machines?
4. Should app01 expose its Clojure app directly or through nginx?
```

Do not blindly close ports. First understand what opened them.

---

## Lab 7: Create a tiny test service with netcat

On app01:

```bash
nc -l 9090
```

From db01:

```bash
nc app01 9090
```

Type text on one side and observe it on the other.

If it fails, check firewall.

AlmaLinux firewall check:

```bash
sudo firewall-cmd --list-all
```

Temporary allow for lab:

```bash
sudo firewall-cmd --add-port=9090/tcp
```

Remove after lab:

```bash
sudo firewall-cmd --remove-port=9090/tcp
```

Ubuntu UFW check:

```bash
sudo ufw status verbose
```

### Questions

```text
1. Which machine was the server?
2. Which machine was the client?
3. What port was used?
4. Was there authentication?
5. Why would this be unsafe as a real service?
```

Lesson:

```text
A reachable port is not automatically a secure service.
```

---

## Lab 8: Install nginx on app01 and observe it

On AlmaLinux/app01:

```bash
sudo dnf install -y nginx
sudo systemctl enable --now nginx
systemctl status nginx
ss -tulpen | grep ':80'
sudo lsof -iTCP:80 -sTCP:LISTEN -P -n
```

Open firewall if needed:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

From db01:

```bash
curl http://app01/
nc -vz app01 80
```

Logs:

```bash
sudo journalctl -u nginx -n 50
sudo ls -l /var/log/nginx
```

### Questions

```text
1. Which process listens on port 80?
2. Which user owns nginx worker processes?
3. Where are nginx logs?
4. Why is nginx usually run as a service instead of from a terminal?
```

---

## Lab 9: Connect the chapter to the Clojure app

Later, when the Clojure app runs on app01:

```bash
systemctl status clojure-crud
ss -tulpen | grep ':3000'
sudo lsof -iTCP:3000 -sTCP:LISTEN -P -n
curl http://127.0.0.1:3000/
```

From db01:

```bash
nc -vz app01 3000
```

### Design decision

Answer:

```text
Should port 3000 be reachable from the LAN, or should nginx expose port 80 and proxy to 127.0.0.1:3000?
```

Preferred answer:

```text
Usually nginx should expose 80/443, and the app should listen on localhost if only nginx needs to reach it.
```

---

## Lab 10: Database service observation

On db01 after MariaDB/MySQL is installed:

```bash
systemctl status mariadb
ss -tulpen | grep ':3306'
sudo lsof -iTCP:3306 -sTCP:LISTEN -P -n
sudo journalctl -u mariadb -n 50
```

From app01:

```bash
nc -vz db01 3306
```

From another machine not allowed by firewall:

```bash
nc -vz db01 3306
```

### Questions

```text
1. Which address is MariaDB bound to?
2. Is it listening on 127.0.0.1 only, db01 LAN IP only, or all addresses?
3. Which machines should be allowed to reach it?
4. Does successful port connection prove database authentication works?
```

---

## Lab 11: Service security worksheet

Fill this out for each service:

```text
Service:
Machine:
Purpose:
Protocol:
Port:
Listening address:
Process name:
Service manager unit:
Linux user running it:
Allowed clients:
Firewall rule:
Log command:
Failure symptom:
How to restart:
What if compromised:
```

Use it for:

```text
sshd
nginx
clojure-crud
mariadb/mysql
```

---

## Lab 12: Final failure drill

Break one thing at a time. After each break, diagnose and fix.

### Break A: stop database

On db01:

```bash
sudo systemctl stop mariadb
```

From app01:

```bash
nc -vz db01 3306
```

Diagnose:

```bash
systemctl status mariadb
ss -tulpen | grep ':3306'
sudo journalctl -u mariadb -n 50
```

Fix:

```bash
sudo systemctl start mariadb
```

### Break B: block database firewall

On db01, temporarily remove/deny port 3306 access. Then from app01:

```bash
nc -vz db01 3306
```

Diagnose with:

```bash
sudo ufw status verbose
sudo tcpdump -i any port 3306
```

Fix the firewall after observing.

### Break C: stop nginx

On app01:

```bash
sudo systemctl stop nginx
```

From db01:

```bash
curl http://app01/
nc -vz app01 80
```

Diagnose:

```bash
systemctl status nginx
ss -tulpen | grep ':80'
sudo journalctl -u nginx -n 50
```

Fix:

```bash
sudo systemctl start nginx
```

## Final rule

Never say:

```text
It does not work.
```

Say:

```text
The client command was ___.
The error was ___.
The server was/was not listening on port ___.
The firewall allowed/blocked ___.
The service log showed ___.
My conclusion is ___.
```

That is how an infrastructure professional thinks.
