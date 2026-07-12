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
# Session 4: Documentation Habits — `man`, `--help`, `type`, `help`, `info`

## Purpose

A serious command-line learner must not depend only on copied examples.

He needs this habit:

```text
When I do not know an option, I check documentation.
```

This session teaches how to use documentation while practicing pipelines.

---

# Feynman analogy: tool labels in a workshop

A beginner asks:

```text
What command do I copy?
```

A stronger learner asks:

```text
What kind of tool is this?
Where is its manual?
Which option answers my question?
```

The manual page is not a novel. It is a reference shelf.

---

# Tool 1: `type`

Question:

```text
Is this command built into the shell, an alias, a function, or an external program?
```

Run:

```bash
type cd
type grep
type sort
type history
type printf
type wc
```

Explain:

```text
type tells me what kind of command name this is.
```

Why this matters:

```text
Shell builtins may use help.
External programs usually use man or --help.
```

---

# Tool 2: `help` for shell builtins

Run:

```bash
help cd
help printf
help type
```

Question:

```text
Why does help cd work but man cd may be less direct?
```

Expected idea:

```text
cd is a shell builtin. Bash help explains bash builtins directly.
```

---

# Tool 3: `man`

Practice with commands used in pipelines:

```bash
man grep
man sort
man uniq
man wc
man cut
```

Inside `man`, practice:

```text
/ignore-case
/count
/numeric
/field
n
N
q
```

Answer:

1. How do you search inside a man page?
2. How do you go to the next match?
3. How do you quit?
4. Which option did you find today?

---

# Tool 4: `--help`

Run:

```bash
grep --help | head -30
sort --help | head -40
uniq --help | head -40
wc --help | head -40
cut --help | head -40
```

Question:

```text
When is --help more convenient than man?
```

Expected answer:

```text
When I need a quick option reminder.
```

---

# Tool 5: `info` when available

Try:

```bash
info coreutils 'sort invocation'
```

If it is not available or confusing, note that.

Question:

```text
Why might info contain more detail than man for GNU tools?
```

Expected idea:

```text
Some GNU tools have fuller documentation in info pages.
```

---

# Documentation-driven drill 1: Find `sort -n`

Question:

```text
How do I sort numbers numerically rather than alphabetically?
```

Do not guess.

Check:

```bash
man sort
```

Search:

```text
/numeric
```

Now test:

```bash
printf '10
2
30
' | sort
printf '10
2
30
' | sort -n
```

Explain:

```text
Default sort is lexical.
sort -n sorts numerically.
```

---

# Documentation-driven drill 2: Find `uniq -c`

Question:

```text
How do I count repeated adjacent lines?
```

Check:

```bash
man uniq
```

Search:

```text
/count
```

Test:

```bash
printf 'apple
apple
banana
' | uniq -c
```

Then test why sort matters:

```bash
printf 'apple
banana
apple
' | uniq -c
printf 'apple
banana
apple
' | sort | uniq -c
```

---

# Documentation-driven drill 3: Find `grep -E`

Question:

```text
How do I use extended regular expressions with grep?
```

Check:

```bash
man grep
```

Search:

```text
/extended
```

Test:

```bash
grep -E 'ERROR|WARN' data/errors.txt
```

Explain:

```text
-E enables extended regular expressions, including alternation with |.
```

---

# Documentation habit checklist

Before asking someone else, he should try at least two:

```text
type COMMAND
COMMAND --help
man COMMAND
help COMMAND
info COMMAND or info coreutils 'COMMAND invocation'
apropos KEYWORD
```

Practice:

```bash
apropos sort | head
apropos checksum | head
```

---

# Session 4 self-check

He understands this session if he can answer:

1. When should I use `type`?
2. When should I use `help`?
3. When should I use `man`?
4. When is `--help` useful?
5. How do I search inside a man page?
6. Which options did I discover from documentation today?
