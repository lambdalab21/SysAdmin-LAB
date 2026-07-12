#Book/The-Linux-Command-Line #Author/Shotts 
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

1. What filename did `wget` choose? index.html
2. Did the file contain HTML? Yes. 
3. Why is `wget` called a non-interactive downloader? It runs without any interactive prompts. This makes it perfect for scripts and automation. 

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
