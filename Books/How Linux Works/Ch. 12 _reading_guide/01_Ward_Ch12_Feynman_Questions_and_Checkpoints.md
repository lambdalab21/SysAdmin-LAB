
---
## Question set 1 — Identity

Answer these in writing:

```text
When I copy a file to app01 as john, who owns the file on app01?
When I copy a file with sudo, who owns it?
Why is copying as root usually a bad habit?
Why might the app service account not be able to read a file copied by john?
```

Experiment:

```bash
# On workstation or db01
mkdir -p ~/ch12-lab
cd ~/ch12-lab
echo "hello" > identity.txt
scp identity.txt john@app01:/home/john/

# On app01
ls -l /home/john/identity.txt
stat /home/john/identity.txt
```

Feynman explanation:

> “The account used for the transfer affects ownership at the destination.”

---

## Question set 2 — Paths

Before running each command, draw the expected result.

```bash
scp file.txt john@app01:/home/john/
scp file.txt john@app01:/home/john/newname.txt
scp -r project john@app01:/home/john/lab/
```

Questions:

```text
Is the destination a file or a directory?
Does the destination already exist?
Will the file keep its name or get renamed?
What happens if the destination path is wrong?
```

Discipline rule:

> Do not run copy commands while vague about the destination path.

---

## Question set 3 — Transfer vs sync

Explain the difference:

```text
scp copies.
rsync synchronizes.
```

That sentence is still too shallow.

Better explanation should include:

```text
rsync can skip unchanged files
rsync can preserve metadata
rsync can show what it would do before doing it
rsync can delete files at the destination if requested
rsync can exclude paths
```

Feynman prompt:

> Teach why rsync is better for repeated deployments than scp.

---

## Question set 4 — Trailing slash

Predict these two commands:

```bash
rsync -av project john@app01:/home/john/rsync-lab/
rsync -av project/ john@app01:/home/john/rsync-lab/
```

He must answer:

```text
Will the destination contain rsync-lab/project/file.txt?
Or will it contain rsync-lab/file.txt?
```

If he cannot predict this, he must not use rsync for deployment yet.

---

## Question set 5 — Dry run

Explain:

```bash
rsync -avn source/ john@app01:/home/john/destination/
```

Questions:

```text
What does -a do?
What does -v do?
What does -n do?
Why should -n be used before dangerous commands?
```

Feynman prompt:

> Explain dry-run as a safety habit, not as a convenience feature.

---

## Question set 6 — Deletes

Danger command:

```bash
rsync -av --delete source/ john@app01:/home/john/destination/
```

Questions:

```text
What does --delete do?
Which side is treated as the source of truth?
What files could disappear?
Why should --dry-run be used first?
```

Required safe version:

```bash
rsync -avn --delete source/ john@app01:/home/john/destination/
```

Feynman prompt:

> Explain why `--delete` is powerful and dangerous.

---

## Question set 7 — Excludes

Explain these:

```bash
rsync -av --exclude '.git/' project/ john@app01:/home/john/project/
rsync -av --exclude 'target/' project/ john@app01:/home/john/project/
rsync -av --exclude '.env' project/ john@app01:/home/john/project/
```

Questions:

```text
Why might .git/ be excluded?
Why might target/ be excluded?
Why should .env usually not be copied casually?
What happens if the exclude pattern is wrong?
```

Feynman prompt:

> Explain the difference between source code, build artifacts, and secrets.

---

## Question set 8 — Compression and bandwidth

Explain:

```bash
rsync -az source/ john@app01:/home/john/source/
rsync -av --bwlimit=1000 source/ john@app01:/home/john/source/
```

Questions:

```text
When does compression help?
When might compression not help much?
Why would you limit bandwidth?
What happens on a slow network?
```

---

## Question set 9 — Pull vs push

Compare:

```bash
# Push
rsync -av backup.sql john@app01:/home/john/backups/

# Pull
rsync -av john@db01:/home/john/backups/ ./db01-backups/
```

Questions:

```text
Which machine initiates the connection?
Which machine needs SSH access to the other?
Which direction is better for backups?
What happens if one machine is compromised?
```

Feynman prompt:

> Explain why backup direction matters for security.

---

## Question set 10 — Sharing

Explain the difference:

```text
scp/sftp = transfer files
rsync = sync directories efficiently
Samba = share files with Windows-style clients
SSHFS = mount a remote directory over SSH
NFS = Unix/Linux network filesystem sharing
cloud storage = managed remote storage/sync service
```

Questions:

```text
Which tools copy files?
Which tools make a remote directory appear local?
Which tools are better for Windows compatibility?
Which tools are risky if permissions are misunderstood?
```

Feynman prompt:

> Explain why file sharing is not the same as backup.

---

## Final oral exam

He should answer these without notes:

```text
1. What is the safest way to test an rsync command before running it?
2. What is the trailing slash problem?
3. Why should app deployments avoid copying secrets casually?
4. How do you check ownership after copying?
5. Why should database backups be tested after transfer?
6. What is the difference between rsync and Samba?
7. Why is NFS not just “scp but automatic”?
8. Why is chmod 777 not a real solution to transfer problems?
9. What does it mean to pull a backup?
10. What should you write in a deployment log after a file transfer?
```

If he cannot answer, reread and redo the labs.
