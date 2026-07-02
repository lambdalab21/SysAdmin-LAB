# Effective Shell Chapter 2: Thinking in Pipelines

This guide is a follow-up to Shotts, *The Linux Command Line*, Chapter 6 on redirection.

Shotts Chapter 6 should have taught the mechanics:

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
# Session 2: Streams and Small Pipelines

## Purpose

This session practices tiny pipelines, but each command must answer a question.

Do not type until the English question is clear.

---

# Feynman analogy: water pipes

Think of stdout as water leaving a hose.

The pipe symbol `|` connects that water into another machine.

```text
command 1 stdout → command 2 stdin
```

But stderr is like a separate warning siren. It does not normally flow through the same pipe.

---

# Documentation habit before practice

Look up one command before using it.

Run:

```bash
man wc
```

Inside `man`, search for line-count option:

```text
/line
```

Quit:

```text
q
```

Also try:

```bash
wc --help | head
```

Answer:

1. Which option counts lines?
2. Which option counts words?
3. Which option counts bytes?
4. Which was faster for quick lookup, `man` or `--help`?

---

# Drill 1: Count selected lines

Question:

```text
How many ERROR lines are in errors.txt?
```

Build one stage at a time.

Stage 1:

```bash
grep ERROR data/errors.txt
```

Inspect:

```text
Did this select the right lines?
```

Stage 2:

```bash
grep ERROR data/errors.txt | wc -l
```

Explain:

```text
grep selected matching lines.
wc -l counted those lines.
```

---

# Drill 2: Count status codes in a log

Question:

```text
Which HTTP status codes appear, and how many times?
```

Start broad:

```bash
cat data/access.log
```

Extract a likely field:

```bash
cut -d ' ' -f9 data/access.log
```

Sort:

```bash
cut -d ' ' -f9 data/access.log | sort
```

Count:

```bash
cut -d ' ' -f9 data/access.log | sort | uniq -c
```

Explain each stage:

```text
cut selects field 9.
sort groups equal values together.
uniq -c counts adjacent equal lines.
```

Discipline question:

```text
Why does uniq -c usually need sort before it?
```

---

# Drill 3: Extract installed package names

Question:

```text
Which packages are marked installed in packages.txt?
```

Stage 1:

```bash
grep '\[installed\]' data/packages.txt
```

Stage 2:

```bash
grep '\[installed\]' data/packages.txt | cut -d '/' -f1
```

Stage 3:

```bash
grep '\[installed\]' data/packages.txt | cut -d '/' -f1 | sort
```

Explain:

```text
The regex selects installed lines.
cut extracts the package name before the slash.
sort orders the names.
```

Question:

```text
Why do the square brackets need escaping?
```

Expected idea:

```text
In regex, [ ] are special. Escaping them matches literal brackets.
```

---

# Drill 4: Avoid fake sophistication

Compare:

```bash
cat data/packages.txt | grep '\[installed\]' | cut -d '/' -f1
```

with:

```bash
grep '\[installed\]' data/packages.txt | cut -d '/' -f1
```

Question:

```text
Which is simpler, and why?
```

Expected answer:

```text
The second is simpler because grep can read the file directly.
```

---

# Session 2 self-check

He understands this session if he can answer:

1. What question does each pipeline answer?
2. What does each stage receive?
3. What does each stage output?
4. Why inspect intermediate output?
5. Which command’s manual/help did he consult?
