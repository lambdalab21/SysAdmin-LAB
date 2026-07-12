#Book/Effective-Shell #Author/Kerr
# Effective Shell Chapter 2: Thinking in Pipelines

This guide is a follow-up to Author/Shotts, *The Linux Command Line*, Chapter 6 on redirection.

Author/Shotts Chapter 6 should have taught the mechanics:

```text
stdin
stdout
stderr
>
>>
<
2>
2>&1
|
tee
```

Kerr Chapter 2 should make him more effective:

```text
Think in streams.
Build pipelines one stage at a time.
Inspect intermediate output.
Use documentation instead of guessing options.
```

Core discipline:

```text
Do not type a pipeline until you can explain what each stage receives and emits.
```

Before every pipeline, fill this out:

```text
Question I am trying to answer:
Input data:
Stage 1 command:
Stage 1 output:
Stage 2 command:
Stage 2 output:
Final output:
How I verified it:
Which command did I check with man/help:
```

One-time setup:

```bash
mkdir -p ~/effective-shell-ch02-pipelines/{data,out,tmp}
cd ~/effective-shell-ch02-pipelines

cat > data/access.log <<'EOF'
192.168.1.10 - - [01/Jun/2026:09:00:01] "GET /index.html" 200 1024
192.168.1.11 - - [01/Jun/2026:09:00:03] "GET /style.css" 200 2048
192.168.1.10 - - [01/Jun/2026:09:00:10] "GET /missing.html" 404 512
192.168.1.12 - - [01/Jun/2026:09:00:12] "POST /login" 302 256
192.168.1.13 - - [01/Jun/2026:09:00:15] "GET /index.html" 200 1024
192.168.1.11 - - [01/Jun/2026:09:00:20] "GET /missing.html" 404 512
EOF

cat > data/packages.txt <<'EOF'
bash/stable,now 5.2 amd64 [installed]
coreutils/stable,now 9.1 amd64 [installed]
curl/stable 8.5 amd64
git/stable,now 2.43 amd64 [installed]
grep/stable,now 3.11 amd64 [installed]
nginx/stable 1.24 amd64
openssh-server/stable,now 9.6 amd64 [installed]
python3/stable,now 3.12 amd64 [installed]
vim/stable,now 9.1 amd64 [installed]
zsh/stable 5.9 amd64
EOF

cat > data/users.tsv <<'EOF'
alice	1001	project
bob	1002	project
charlie	1003	guest
diana	1004	admin
elliot	1005	project
EOF

cat > data/errors.txt <<'EOF'
INFO started
ERROR failed login alice
WARN disk almost full
ERROR timeout db01
INFO stopped
ERROR failed login bob
EOF
```

Verify:

```bash
find ~/effective-shell-ch02-pipelines -maxdepth 3 -type f -print
```

---
# Session 5: Applied Lab and Self-Test

## Purpose

This session tests whether he can think in pipelines without copying blindly.

Rules:

```text
No long pipeline first.
No notes for the first attempt.
Use man/help when stuck.
Explain the final answer.
```

---

# Lab template

For every task:

```text
Question:
Input file or command:
First stage:
What first stage outputs:
Second stage:
What second stage outputs:
Final command:
Verification:
Documentation checked:
```

---

# Task 1: Count log levels

Question:

```text
How many INFO, WARN, and ERROR lines are in errors.txt?
```

Expected build:

```bash
cut -d ' ' -f1 data/errors.txt
cut -d ' ' -f1 data/errors.txt | sort
cut -d ' ' -f1 data/errors.txt | sort | uniq -c
```

Explain:

```text
cut extracts the level.
sort groups levels together.
uniq -c counts adjacent repeated levels.
```

---

# Task 2: Installed package report

Question:

```text
Create a sorted list of installed package names.
```

Expected build:

```bash
grep '\[installed\]' data/packages.txt
grep '\[installed\]' data/packages.txt | cut -d '/' -f1
grep '\[installed\]' data/packages.txt | cut -d '/' -f1 | sort
```

Save:

```bash
grep '\[installed\]' data/packages.txt | cut -d '/' -f1 | sort > out/installed-packages.txt
```

Verify:

```bash
cat out/installed-packages.txt
```

---

# Task 3: HTTP status-code report

Question:

```text
Which status codes appear in access.log and how often?
```

Expected build:

```bash
cut -d ' ' -f9 data/access.log
cut -d ' ' -f9 data/access.log | sort
cut -d ' ' -f9 data/access.log | sort | uniq -c
```

Save:

```bash
cut -d ' ' -f9 data/access.log | sort | uniq -c > out/status-codes.txt
```

Verify:

```bash
cat out/status-codes.txt
```

---

# Task 4: Most common IP address

Question:

```text
Which IP address appears most often in access.log?
```

Expected build:

```bash
cut -d ' ' -f1 data/access.log
cut -d ' ' -f1 data/access.log | sort
cut -d ' ' -f1 data/access.log | sort | uniq -c
cut -d ' ' -f1 data/access.log | sort | uniq -c | sort -n
```

Optional final stage:

```bash
cut -d ' ' -f1 data/access.log | sort | uniq -c | sort -n | tail -1
```

Documentation challenge:

```text
Use man tail or tail --help to find how to show only one line.
```

---

# Task 5: Project-group usernames

Question:

```text
Which users are in the project group?
```

Expected build:

```bash
grep $'	project$' data/users.tsv
grep $'	project$' data/users.tsv | cut -f1
grep $'	project$' data/users.tsv | cut -f1 | sort
```

Explain why this is weaker:

```bash
grep project data/users.tsv
```

Expected idea:

```text
It matches the word project anywhere, not specifically the final group field.
```

---

# Task 6: Debug a bad pipeline

Bad pipeline:

```bash
cat data/access.log | cut -d ' ' -f9 | uniq -c
```

Questions:

1. Is `cat` needed?
2. Why might `uniq -c` give misleading counts?
3. What stage is missing?

Better:

```bash
cut -d ' ' -f9 data/access.log | sort | uniq -c
```

---

# Task 7: Explain without running

Explain this pipeline before running it:

```bash
grep ' 404 ' data/access.log | cut -d ' ' -f7 | sort | uniq -c | sort -n
```

Use:

```text
grep stage:
cut stage:
sort stage 1:
uniq stage:
sort stage 2:
Final question answered:
Weak assumption:
```

Then run it.

Question:

```text
Was your explanation accurate?
```

---

# Final self-test

Without notes, write commands for:

1. Count ERROR lines.
2. List installed package names.
3. Count status codes.
4. Count IP addresses.
5. Save a report to a file.
6. Show a report and save it at the same time with `tee`.
7. Find the option for numeric sort using documentation.
8. Find the option for counting duplicates with documentation.

Expected command patterns:

```bash
grep ERROR data/errors.txt | wc -l
grep '\[installed\]' data/packages.txt | cut -d '/' -f1 | sort
cut -d ' ' -f9 data/access.log | sort | uniq -c
cut -d ' ' -f1 data/access.log | sort | uniq -c
COMMAND > out/report.txt
COMMAND | tee out/report.txt
man sort
man uniq
```

---

# End standard

He understands Kerr Chapter 2 only if he can say:

```text
I build pipelines from a question, not from memorized tricks.
I inspect each stage before adding the next.
I know stdout flows through pipes but stderr usually does not.
I can explain why sort often comes before uniq.
I can use man, --help, type, help, and apropos to discover options.
I can simplify unnecessary pipelines.
```
