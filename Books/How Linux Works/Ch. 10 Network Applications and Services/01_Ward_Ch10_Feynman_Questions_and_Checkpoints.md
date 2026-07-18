#Author/Ward #Book/How-Linux-Works 
#network 
# Ward Ch. 10 — Feynman Questions and Checkpoints

Use this file while reading. Stop at each checkpoint and explain the idea in plain language.

## Rule

Do not write definitions that sound copied from a book. Write like you are teaching a 12-year-old who has used a browser but has never administered a server.

## Checkpoint 1: What is a service?

### Explain

Write your answer:

```text
A service is...
```

Now improve it using these words:

```text
process
background
request
client
server
port
```

### Test yourself

Which of these are services?

```text
sshd
nginx
mysql/mariadb
firefox
ls
ping
```

For each one, explain why or why not.

## Checkpoint 2: Client vs server

Explain this without jargon first:

```text
A client starts the conversation. A server waits for requests.
```

Now make it technical:

```text
A network server process listens on an address and port. A client process connects to that address and port.
```

Questions:

```text
1. When you run `ssh john@app01`, which program is the client?
2. Which program is the server?
3. Which machine must have port 22 reachable?
4. If sshd is stopped, what should the client see?
```

## Checkpoint 3: What does “listening” mean?

Bad explanation:

```text
The server is waiting.
```

Better explanation:

```text
A process has asked the kernel to accept incoming connections on a specific protocol/address/port.
```

Now explain this command:

```bash
ss -tulpen
```

Use these words:

```text
TCP
UDP
listening
PID
program name
local address
port
```

## Checkpoint 4: SSH and sshd

Fill in:

```text
ssh is...
sshd is...
port 22 is...
```

Answer:

```text
Why does `systemctl status sshd` make sense on the server but not necessarily on the client?
```

Ubuntu note:

```bash
systemctl status ssh
```

AlmaLinux note:

```bash
systemctl status sshd
```

## Checkpoint 5: Logs are evidence

A beginner says:

```text
SSH is broken.
```

An operator says:

```text
I tried connecting from X to Y at time Z. The client showed ____. The server log showed ____. Port 22 was/was not listening. The firewall allowed/blocked it.
```

Write a troubleshooting note using that form.

Useful commands:

Ubuntu:

```bash
journalctl -u ssh -n 50
```

AlmaLinux:

```bash
journalctl -u sshd -n 50
```

## Checkpoint 6: lsof

Explain:

```text
lsof means...
```

Use it to answer:

```text
Which process owns this listening port?
```

Commands:

```bash
sudo lsof -i -P -n
sudo lsof -iTCP -sTCP:LISTEN -P -n
```

Write down one line of output and explain every column you understand.

## Checkpoint 7: tcpdump

Explain:

```text
tcpdump lets me see...
```

Important warning:

```text
tcpdump shows packets. It does not automatically explain the application-level meaning.
```

Question:

```text
If `nc -vz db01 3306` succeeds, what should tcpdump show on db01?
```

## Checkpoint 8: netcat / nc

Explain:

```text
nc is useful because...
```

Use it to test whether a port is reachable:

```bash
nc -vz app01 22
nc -vz db01 3306
```

Questions:

```text
1. Does a successful nc test prove the application login will work?
2. What does it prove?
3. What does a timeout suggest?
4. What does connection refused suggest?
```

Expected reasoning:

```text
Success proves TCP reachability to the port. It does not prove credentials, protocol correctness, or application health.
```

## Checkpoint 9: Port scanning

Explain:

```text
Port scanning asks...
```

Use only on machines you own or have permission to test.

Commands:

```bash
nmap app01
nmap db01
```

Questions:

```text
1. Which ports are open?
2. Which ports surprised you?
3. Should MySQL/MariaDB be open to every machine on the LAN?
4. What should the firewall allow?
```

## Checkpoint 10: Security mindset

For each service, fill this out:

```text
Service name:
Purpose:
Port:
Which user runs it:
Who should be allowed to connect:
Authentication method:
Log location / journal command:
Firewall rule:
What happens if this service is compromised:
```

Use this for:

```text
sshd
nginx
clojure app
mysql/mariadb
```

## Final oral exam

Explain this scenario without notes:

```text
A Clojure app on app01 connects to MariaDB on db01. The connection fails. How do you determine whether the problem is DNS/hostname, network routing, firewall, listening port, service process, credentials, or database permissions?
```

Your answer must include at least these checks:

```bash
ping db01
getent hosts db01
ip route
nc -vz db01 3306
ss -tulpen
journalctl -u mariadb -n 50
sudo lsof -i -P -n
sudo tcpdump -i any port 3306
```

Do not guess. Gather evidence.
