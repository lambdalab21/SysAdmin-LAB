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
