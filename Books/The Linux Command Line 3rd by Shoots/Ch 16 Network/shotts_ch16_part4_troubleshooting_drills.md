#Book/The-Linux-Command-Line  #Author/Shotts 
#network 
# Author/Shotts Chapter 16 — Networking Drills

These drills accompany Chapter 16, **Networking**, of William Author/Shotts' *The Linux Command Line*.
Use machines that belong to your home lab, such as `app01` and `db01`.

## Safety and setup

- Do not scan public hosts, school networks, neighbors' networks, or networks you do not administer.
- Replace `app01`, `db01`, and `<user>` with actual hostnames, IP addresses, and usernames from the lab.
- Some commands may need to be installed separately depending on the distribution.
- Prefer `tracepath` if `traceroute` is unavailable.
- Prefer `ss` for normal work. Use `netstat` only as a historical comparison if it is already installed.
- Read the `ftp` section for historical context, but do not set up or use plaintext FTP for normal file transfer.

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

| `ping`   | `ssh`    | Likely interpretation                                                                         |
| -------- | -------- | --------------------------------------------------------------------------------------------- |
| succeeds | succeeds | Host and SSH service are reachable                                                            |
| succeeds | fails    | Host may be reachable, but SSH service, port, authentication, or firewall needs investigation |
| fails    | succeeds | ICMP may be blocked even though SSH works                                                     |
| fails    | fails    | Gather more evidence before concluding that the host is down                                  |

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

Author/Shotts' networking chapter remains useful because the underlying questions have not changed, but some command preferences have changed.

| Historical or older emphasis | Prefer for current Linux practice |
|---|---|
| `netstat -r` | `ip route` |
| `netstat -tulpn` | `ss -tulpn` |
| plaintext `ftp` | `sftp`, `scp`, or another encrypted transfer method |
| `traceroute` | `traceroute` or `tracepath`, depending on what is installed |

The goal is not to memorize every option. The goal is to know which tool answers the next troubleshooting question.