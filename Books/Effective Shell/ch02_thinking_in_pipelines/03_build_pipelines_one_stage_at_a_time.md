#Book/Effective-Shell #Author/Kerr 
#pipeline 
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
# Session 3: Build Pipelines One Stage at a Time

## Purpose

This session prevents the worst beginner habit:

```text
copy a long pipeline without understanding it
```

The rule:

```text
Never add stage 2 until stage 1 has been inspected.
Never add stage 3 until stage 2 has been inspected.
```

---

# Feynman analogy: cooking one step at a time

A recipe is not one magic spell.

```text
wash vegetables
cut vegetables
heat pan
cook vegetables
season food
```

A pipeline is the same.

```text
select rows
extract fields
sort values
count duplicates
format output
```

If one step is wrong, the final dish is wrong.

---

# Documentation habit: inspect `cut`

Run:

```bash
man cut
```

Search inside the man page:

```text
/delimiter
```

Then:

```bash
cut --help | head -30
```

Answer:

1. Which option sets the delimiter?
2. Which option selects fields?
3. What is the default delimiter for fields?
4. Why does `cut -f1` work well on TSV files?

---

# Lab 1: Project users from TSV

Question:

```text
Which users are in the project group?
```

Stage 0: inspect raw data.

```bash
cat data/users.tsv
```

Stage 1: select project rows.

```bash
grep $'	project$' data/users.tsv
```

Stage 2: extract usernames.

```bash
grep $'	project$' data/users.tsv | cut -f1
```

Stage 3: sort.

```bash
grep $'	project$' data/users.tsv | cut -f1 | sort
```

Explain:

```text
The grep pattern matches lines ending with a tab followed by project.
cut -f1 extracts the first tab-separated field.
sort orders the usernames.
```

Discipline question:

```text
Why is grep 'project' less precise than grep $'	project$'?
```

---

# Lab 2: Top IP addresses from access log

Question:

```text
Which IP addresses appear most often?
```

Stage 0:

```bash
head data/access.log
```

Stage 1: extract IP address.

```bash
cut -d ' ' -f1 data/access.log
```

Stage 2: sort IP addresses.

```bash
cut -d ' ' -f1 data/access.log | sort
```

Stage 3: count repeated IPs.

```bash
cut -d ' ' -f1 data/access.log | sort | uniq -c
```

Stage 4: sort counts numerically.

```bash
cut -d ' ' -f1 data/access.log | sort | uniq -c | sort -n
```

Question:

```text
What does the final line represent?
```

---

# Lab 3: HTTP 404 report

Question:

```text
Which requested paths produced 404 errors?
```

Stage 1:

```bash
grep ' 404 ' data/access.log
```

Stage 2:

```bash
grep ' 404 ' data/access.log | cut -d ' ' -f7
```

Stage 3:

```bash
grep ' 404 ' data/access.log | cut -d ' ' -f7 | sort | uniq -c
```

Explain:

```text
grep selects 404 lines.
cut extracts the request path.
sort groups identical paths.
uniq -c counts them.
```

---

# Lab 4: Save a report safely

Question:

```text
Can I create a status-code summary report?
```

Preview first:

```bash
cut -d ' ' -f9 data/access.log | sort | uniq -c
```

Then save:

```bash
cut -d ' ' -f9 data/access.log | sort | uniq -c > out/status-code-summary.txt
```

Verify:

```bash
cat out/status-code-summary.txt
```

Explain:

```text
The pipeline creates the report.
> writes stdout into a file.
cat verifies the saved report.
```

---

# Session 3 self-check

For each pipeline, identify:

```text
Question:
Input:
Stage 1:
Stage 2:
Stage 3:
Final answer:
Weakest assumption:
```

He is not done if he cannot name the weakest assumption.
