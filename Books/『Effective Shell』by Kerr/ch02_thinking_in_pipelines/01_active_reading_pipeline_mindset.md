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
# Session 1: Active Reading and Pipeline Mindset

## Purpose

Use this while reading Kerr Chapter 2.

The goal is not to collect pipeline tricks. The goal is to learn a way of thinking:

```text
A command receives a stream, transforms it, and emits another stream.
```

---

# Feynman analogy: the assembly line

Imagine an assembly line.

Each worker does one small job:

```text
Worker 1 selects only error lines.
Worker 2 selects the username column.
Worker 3 sorts the names.
Worker 4 counts duplicates.
```

That is a pipeline.

```bash
grep ERROR data/errors.txt | cut -d ' ' -f4 | sort | uniq -c
```

But a disciplined worker does not build the entire factory at once.

He tests one worker at a time.

---

# Reading checkpoint 1: What is a pipeline?

Read the opening part of Kerr Chapter 2.

Stop and answer:

1. What problem does a pipeline solve?
2. What does the pipe symbol `|` connect?
3. Does the pipe connect files or streams?
4. Why is a pipeline different from saving intermediate files manually?

Explain-back:

```text
A pipeline sends stdout from one command into stdin of the next command.
```

---

# Reading checkpoint 2: Connect to Author/Shotts Chapter 6

Before continuing, review these from Author/Shotts:

```text
stdout = normal output
stderr = error output
stdin  = normal input
|      = connect stdout to stdin
>      = redirect stdout to file
2>     = redirect stderr to file
```

Answer:

1. Does a pipe normally carry stderr?
2. What stream does the next command usually receive?
3. Why can error messages still appear on screen during a pipeline?

Practice:

```bash
cd ~/effective-shell-ch02-pipelines
ls data missing-directory | wc -l
ls data missing-directory 2>/dev/null | wc -l
```

Explain:

```text
The first command lets stderr appear on screen.
The second command sends stderr to /dev/null before wc receives stdout.
```

---

# Reading checkpoint 3: The pipeline notebook

For this chapter, keep a small notebook or Markdown file.

Use this template:

```text
Pipeline question:
Raw input command:
What stage 1 should output:
What stage 1 actually output:
What stage 2 should output:
What stage 2 actually output:
Final answer:
Documentation checked:
```

The point is to train thinking, not command copying.

---

# First tiny pipeline

Question:

```text
How many lines are in errors.txt?
```

Predict first:

```text
Which command prints the file?
Which command counts lines?
Which stream connects them?
```

Run:

```bash
cat data/errors.txt
cat data/errors.txt | wc -l
wc -l data/errors.txt
```

Explain:

```text
cat file | wc -l works, but wc -l file is simpler.
Pipelines are useful, but not every task needs a pipe.
```

Discipline question:

```text
When is a pipeline unnecessary?
```

Expected answer:

```text
When one command can directly read the file and answer the question clearly.
```

---

# Session 1 self-check

He understands this session if he can answer:

1. What does `|` connect?
2. Does a pipe normally send stderr?
3. Why should pipelines be built one stage at a time?
4. Why is `cat file | wc -l` often unnecessary?
5. What is the English question behind a pipeline?
