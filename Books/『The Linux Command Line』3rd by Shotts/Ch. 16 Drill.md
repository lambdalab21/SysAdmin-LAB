# Shotts Chapter 16 — Networking Drills

These drills accompany Chapter 16, **Networking**, of William Shotts' *The Linux Command Line*.
Use machines that belong to your home lab, such as `app01` and `db01`.

## Safety and setup

- Do not scan public hosts, school networks, neighbors' networks, or networks you do not administer.
- Replace `app01`, `db01`, and `<user>` with actual hostnames, IP addresses, and usernames from the lab.
- Some commands may need to be installed separately depending on the distribution.
- Prefer `tracepath` if `traceroute` is unavailable.
- Prefer `ss` for normal work. Use `netstat` only as a historical comparison if it is already installed.
- Read the `ftp` section for historical context, but do not set up or use plaintext FTP for normal file transfer.

---

# Part 1 — Examine and monitor the network

## Drill 1: Verify local TCP/IP operation

**Time:** 3 minutes

```bash
ping -c 4 localhost
ping -c 4 127.0.0.1
```

**Task:**
Confirm that both commands work. Explain what this test verifies and what it does **not** verify.

**Check your answer:**
Both commands test the local loopback interface. Success does not prove that the Ethernet or Wi-Fi interface, router, DNS server, or Internet connection is working.

---

## Drill 2: Test another machine on the LAN

**Time:** 4 minutes

```bash
ping -c 4 app01
ping -c 4 <app01-IP-address>
```

**Task:**
Compare the hostname-based test with the IP-address-based test.

**Questions:**

1. If both succeed, what has been verified?
2. If the IP-address test succeeds but the hostname test fails, what should you investigate?
3. Can a failed `ping` prove that the host is down?

**Check your answer:**
A successful hostname-based ping shows that name resolution and ICMP reachability both work. If the IP-address test works but the hostname test fails, investigate hostname resolution. A failed ping does not prove that the host is down because firewalls and hosts can block ICMP traffic.

---

## Drill 3: Interpret ping statistics

**Time:** 4 minutes

```bash
ping -c 10 app01
```

**Task:**
Record:

- packets transmitted;
- packets received;
- packet-loss percentage;
- minimum, average, and maximum round-trip time.

**Question:**
Which result would concern you more on a wired LAN: a slightly changing response time or packet loss?

**Check your answer:**
Packet loss on a small wired LAN deserves investigation. Small round-trip-time variation is usually less concerning.

---

## Drill 4: Trace the path to a destination

**Time:** 5 minutes

Try one of these:

```bash
tracepath example.com
```

or:

```bash
traceroute example.com
```

**Task:**
Identify:

- the first hop;
- the number of visible hops;
- any lines containing `*`;
- the final destination, if reached.

**Questions:**

1. What is the first hop likely to be on a home network?
2. Does `* * *` automatically prove that the route is broken?

**Check your answer:**
The first hop is commonly the local router or default gateway. Asterisks can mean that a router did not answer the diagnostic probe; they do not automatically prove that traffic cannot pass through it.

---

## Drill 5: List interfaces and addresses

**Time:** 4 minutes

```bash
ip -br addr
ip link
```

**Task:**
Identify:

- the loopback interface;
- the active Ethernet or Wi-Fi interface;
- its IPv4 address;
- its CIDR prefix;
- whether the interface is `UP`.

**Check your answer:**
`lo` is the loopback interface. The active network interface should normally have an address and show an enabled state.

---

## Drill 6: Inspect the routing table

**Time:** 5 minutes

```bash
ip route
```

**Task:**
Find:

- the local-network route;
- the default route;
- the default gateway;
- the interface used by the default route.

Then run:

```bash
ip route get <app01-IP-address>
ip route get 8.8.8.8
```

**Question:**
Why may the route to `app01` differ from the route to `8.8.8.8`?

**Check your answer:**
A host on the same LAN can usually be reached directly through the local interface. An external address normally goes through the default gateway.

---

## Drill 7: Inspect listening sockets with the modern tool

**Time:** 5 minutes

```bash
ss -tulpn
sudo ss -tulpn
```

**Task:**
Find:

- the SSH service;
- nginx or another web service, if one is running;
- the protocol;
- the local address;
- the port;
- the process name, where visible.

**Questions:**

1. Why may `sudo` reveal more process information?
2. What is the difference between a listening socket and an established connection?

**Check your answer:**
Ordinary users may not be allowed to see complete process details for every socket. A listening socket waits for incoming connections; an established connection represents an active conversation.

---

## Drill 8: Compare `ss` with historical `netstat`

**Time:** 5 minutes

Run this only if `netstat` is already installed:

```bash
netstat -tulpn
netstat -r
```

Compare with:

```bash
ss -tulpn
ip route
```

**Task:**
Map the older command to its modern replacement.

| Older command | Modern replacement |
|---|---|
| `netstat -tulpn` | `ss -tulpn` |
| `netstat -r` | `ip route` |

**Question:**
Why is it still useful to recognize `netstat` even if you normally use `ss` and `ip`?

**Check your answer:**
Older documentation, scripts, systems, and troubleshooting discussions still refer to `netstat`. Recognition is useful; habitual use is not necessary.

---

# Part 2 — Download files over a network

## Drill 9: Download a file with `wget`

**Time:** 5 minutes

Create a clean practice directory:

```bash
mkdir -p ~/lab/shotts-ch16-wget
cd ~/lab/shotts-ch16-wget
```

Download a small public text file:

```bash
wget https://www.example.com/
```

**Task:**
List the downloaded file and inspect its type:

```bash
ls -lh
file index.html
head index.html
```

**Questions:**

1. What filename did `wget` choose?
2. Did the file contain HTML?
3. Why is `wget` called a non-interactive downloader?

**Check your answer:**
`wget` downloads without requiring an interactive session with the remote server. It is useful in scripts and repeatable command-line workflows.

---

## Drill 10: Use an explicit output filename

**Time:** 3 minutes

```bash
cd ~/lab/shotts-ch16-wget
wget -O example-homepage.html https://www.example.com/
ls -lh
```

**Task:**
Confirm that the output file uses the name you chose.

**Question:**
Why is `-O` useful in scripts?

**Check your answer:**
A predictable filename makes later scripted steps simpler and less fragile.

---

## Drill 11: Read about FTP without using it for normal work

**Time:** 5 minutes

Read the FTP portion of the chapter.

**Task:**
Write a two-sentence explanation answering:

1. Why was FTP historically important?
2. Why should ordinary plaintext FTP not be used for usernames, passwords, or sensitive files?

**Check your answer:**
FTP was a common file-transfer protocol before modern secure alternatives became standard. Traditional FTP sends credentials and data without encryption, so use SSH-based alternatives such as `sftp` or `scp` for lab work.

---

# Part 3 — Secure communication with remote hosts

## Drill 12: Connect to a remote host with SSH

**Time:** 5 minutes

```bash
ssh <user>@app01
```

**Task:**
After logging in, run:

```bash
hostname
whoami
pwd
exit
```

**Questions:**

1. Which machine executes `hostname` after login?
2. Which user account is active remotely?
3. Why is SSH preferable to older plaintext remote-login tools?

**Check your answer:**
Commands run on the remote host after login. SSH authenticates the remote host and encrypts communication.

---

## Drill 13: Inspect the first-connection host-key prompt

**Time:** 5 minutes

Use a host that the client has not connected to before, such as another lab VM:

```bash
ssh <user>@db01
```

**Task:**
Read the host-key message carefully before accepting it.

Then inspect the local record:

```bash
grep db01 ~/.ssh/known_hosts
```

The hostname may be hashed, depending on SSH configuration. If so, use:

```bash
ssh-keygen -F db01
```

**Questions:**

1. Why does SSH warn you during the first connection?
2. What security problem is the host key intended to reduce?
3. Should a changed host-key warning be ignored automatically?

**Check your answer:**
The client has not previously verified the remote machine's identity. Host keys help detect impersonation and man-in-the-middle attacks. A changed key can be legitimate after a rebuild, but it must be investigated rather than blindly accepted.

---

## Drill 14: Run a single remote command

**Time:** 4 minutes

```bash
ssh <user>@app01 hostname
ssh <user>@app01 'uname -r'
ssh <user>@app01 'ss -ltn'
```

**Task:**
Explain the difference between opening an interactive shell and running one remote command.

**Check your answer:**
SSH can execute a command remotely and return its output without opening an interactive session. This becomes useful in scripts and administrative checks.

---

## Drill 15: Copy a file to a remote host with `scp`

**Time:** 5 minutes

```bash
mkdir -p ~/lab/shotts-ch16-scp
printf 'Shotts Chapter 16 scp drill\n' > ~/lab/shotts-ch16-scp/message.txt

scp ~/lab/shotts-ch16-scp/message.txt <user>@app01:/tmp/
ssh <user>@app01 'cat /tmp/message.txt'
```

**Task:**
Confirm that the local file arrived on the remote host.

**Question:**
What does the colon in `<user>@app01:/tmp/` mean?

**Check your answer:**
The colon separates the remote host from the remote path.

---

## Drill 16: Copy a remote file back to the local machine

**Time:** 5 minutes

```bash
mkdir -p ~/lab/shotts-ch16-scp/download
scp <user>@app01:/etc/hostname ~/lab/shotts-ch16-scp/download/app01-hostname.txt
cat ~/lab/shotts-ch16-scp/download/app01-hostname.txt
```

**Task:**
Confirm that the remote file was copied locally under the chosen name.

**Question:**
How does the source-and-destination order differ from Drill 15?

**Check your answer:**
In Drill 15, the local path is the source and the remote path is the destination. Here, the remote path is the source and the local path is the destination.

---

## Drill 17: Transfer files interactively with `sftp`

**Time:** 7 minutes

```bash
sftp <user>@app01
```

At the `sftp>` prompt, run:

```text
pwd
lpwd
ls
lls
cd /tmp
lcd ~/lab/shotts-ch16-scp
put message.txt
ls
get message.txt downloaded-message.txt
bye
```

**Task:**
Explain the difference between:

- `pwd` and `lpwd`;
- `ls` and `lls`;
- `cd` and `lcd`;
- `put` and `get`.

**Check your answer:**
Commands beginning with `l` apply to the local machine. The other directory and listing commands apply to the remote machine. `put` uploads; `get` downloads.

---

## Drill 18: Verify that `scp` and `sftp` depend on SSH

**Time:** 5 minutes

On `app01`, inspect the SSH service:

```bash
sudo systemctl status sshd
```

On Ubuntu or Debian systems, the service name may be:

```bash
sudo systemctl status ssh
```

Then inspect the listening socket:

```bash
sudo ss -ltnp | grep ':22'
```

**Task:**
Explain why `sftp` can work without running a separate traditional FTP server.

**Check your answer:**
`sftp` uses the SSH service and its encrypted channel. It is not traditional FTP with encryption added.

---

# Part 4 — Troubleshooting drills

## Drill 19: Distinguish host reachability from service reachability

**Time:** 5 minutes

```bash
ping -c 3 app01
ssh <user>@app01
```

**Task:**
Describe the meaning of each case:

| `ping` | `ssh` | Likely interpretation |
|---|---|---|
| succeeds | succeeds | Host and SSH service are reachable |
| succeeds | fails | Host may be reachable, but SSH service, port, authentication, or firewall needs investigation |
| fails | succeeds | ICMP may be blocked even though SSH works |
| fails | fails | Gather more evidence before concluding that the host is down |

**Check your answer:**
Different tools test different assumptions. Do not treat one failed test as a complete diagnosis.

---

## Drill 20: Troubleshoot an SSH failure systematically

**Time:** 10 minutes

From the client:

```bash
getent hosts app01
ping -c 3 app01
ip route get <app01-IP-address>
nc -vz app01 22
ssh -v <user>@app01
```

From the physical console or another working session on `app01`:

```bash
sudo ss -ltnp | grep ':22'
sudo systemctl status sshd
sudo journalctl -u sshd --since '15 minutes ago'
```

On Ubuntu or Debian, replace `sshd` with `ssh` where appropriate.

**Task:**
Before running each command, state:

1. the hypothesis being tested;
2. the expected result;
3. what the actual result means;
4. the next question.

**Check your answer:**
This is the main professional habit: narrow the problem with evidence instead of changing configuration randomly.

---

# Recurring five-minute subset

Repeat these after one week and again after one month:

```bash
ping -c 3 app01
tracepath example.com
ip -br addr
ip route
ss -tulpn
ssh <user>@app01 hostname
scp ~/lab/shotts-ch16-scp/message.txt <user>@app01:/tmp/
```

For each command, explain what question it answers.

---

# Modernization notes

Shotts' networking chapter remains useful because the underlying questions have not changed, but some command preferences have changed.

| Historical or older emphasis | Prefer for current Linux practice |
|---|---|
| `netstat -r` | `ip route` |
| `netstat -tulpn` | `ss -tulpn` |
| plaintext `ftp` | `sftp`, `scp`, or another encrypted transfer method |
| `traceroute` | `traceroute` or `tracepath`, depending on what is installed |

The goal is not to memorize every option. The goal is to know which tool answers the next troubleshooting question.