#Author/Ward #Book/How-Linux-Works 
#network 
# Ward Ch. 10 Reading Guide — Network Applications and Services

Book: Brian Ward, *How Linux Works*, 3rd ed.  
Chapter: 10, “Network Applications and Services”

## Purpose

This chapter is not mainly about memorizing commands. It teaches a system operator's way of thinking:

> A network service is a process, owned by a user, listening on an address and port, controlled by the OS, reachable through the network, and observable through logs and diagnostic tools.

If you finish the chapter and only remember command names, you missed the point.

## Lab context

Use two machines:

```text
app01 = AlmaLinux/Rocky application server
 db01 = Ubuntu/Debian database server
```

Use two accounts where possible:

```text
john        = normal non-admin user
john-admin  = admin user with sudo
```

Do most exploration as `john`. Use `john-admin` only when you need `sudo`.

## Chapter 10 mental model

Before reading, write your best explanation of these words without looking them up:

```text
service:
server:
client:
port:
socket:
daemon:
sshd:
firewall:
log:
```

Then read the chapter and revise your definitions.

A good final explanation should sound like this:

> A service is a long-running program that provides something to other programs or users. A network service usually listens on a port. A client connects to that address and port. The OS tracks the connection as sockets. systemd often starts and supervises services. Logs and diagnostic tools help us prove what is happening.

## Reading checkpoints

### 10.1 The basics of services

Read for this question:

> What does it mean for a service to “listen”?

Do not answer with “it waits for connection.” That is too vague. Mention process, address, port, and protocol.

After reading, answer:

```text
1. What is the difference between a server program and a client program?
2. Why does a server need a port?
3. What would happen if two programs tried to listen on the same IP address and port?
4. Is SSH a protocol, a client command, a server program, or more than one of these?
```

### 10.2 A closer look

Read for this question:

> How does the kernel help network applications communicate?

Write a Feynman explanation to a younger student:

```text
A network program does not throw bytes directly onto the cable. Instead...
```

Your explanation must use these words correctly:

```text
process
kernel
socket
TCP
port
```

### 10.3 Network servers: SSH and sshd

Read for this question:

> What is the difference between `ssh` and `sshd`?

Required answer:

```text
ssh  = client program
sshd = server daemon that listens for SSH connections
```

Then explain:

```text
1. Which machine runs ssh?
2. Which machine runs sshd?
3. What port does sshd normally listen on?
4. Why should root login usually be disabled?
5. Why are SSH keys better than password-only login?
```

### fail2ban

Read for this question:

> What problem does fail2ban try to reduce?

Do not overstate it. fail2ban is not “security.” It is one defensive layer against repeated failed login attempts.

Answer:

```text
1. What log does fail2ban watch?
2. What behavior looks suspicious?
3. What action can fail2ban take?
4. Why is fail2ban less important on a home LAN than on an internet-exposed server?
```

### 10.5 Diagnostic tools

This is the practical center of the chapter.

For each tool, write what it proves:

```text
lsof proves:
tcpdump proves:
netcat/nc proves:
nmap proves:
ss proves:
journalctl proves:
```

Bad answer:

```text
lsof shows stuff.
```

Good answer:

```text
lsof can show which process has a network socket or file open.
```

### Remote procedure calls

Read lightly for now. Do not get stuck.

Main question:

> Why might a program want to call a procedure on another machine as if it were local?

This becomes more important later when learning NFS, distributed systems, databases, APIs, and microservices.

### Network security

Read for this question:

> What can go wrong when a service is reachable from the network?

Answer in categories:

```text
bad authentication:
bad authorization:
buggy server software:
misconfiguration:
unencrypted traffic:
excessive privileges:
exposed ports:
```

### Sockets and Unix domain sockets

Read for this question:

> Why are sockets not only “internet things”?

Final explanation:

```text
Network sockets allow communication across a network.
Unix domain sockets allow processes on the same machine to communicate through the filesystem/socket interface.
```

## End-of-chapter Feynman test

Without looking at the book, explain this system:

```text
Laptop A runs ssh.
Laptop B runs sshd.
Laptop B has port 22 open.
The firewall allows Laptop A to connect.
sshd authenticates the user.
The login becomes a shell process.
journalctl records login events.
```

Your explanation must mention:

```text
client
server
process
daemon
port
firewall
authentication
logs
```

If you cannot explain this, reread the SSH and diagnostic sections.

## Do not skip the discipline

For every command in the chapter, ask:

```text
What question does this command answer?
What evidence does it give me?
Could this output mislead me?
What should I check next?
```

That is infrastructure thinking.
