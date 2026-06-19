# The Linux Command Line, Chapter 20: Text Processing

Use these files as a one-week plan. Each day has a reading target, a Feynman-style analogy, questions, and command practice.

Core habit:

```text
Do not memorize commands as isolated tricks. Use each command to answer a clear question about text.
```

Disciplined thinking pattern:

```text
1. What question am I trying to answer?
2. What text do I have?
3. What part of the text matters?
4. What command transforms or filters it?
5. How can I inspect the result before trusting it?
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch20-week/{unit1,unit2,unit3,unit4,unit5,apply}
cd ~/tlcl-ch20-week

cat > sample.log <<'EOF'
INFO 2026-06-01 app01 started
ERROR 2026-06-01 app01 failed-login alice
INFO 2026-06-01 app01 request /index.html
WARN 2026-06-01 db01 disk-space-low
ERROR 2026-06-02 db01 timeout
ERROR 2026-06-02 app01 failed-login bob
INFO 2026-06-02 app01 stopped
EOF

cat > users.tsv <<'EOF'
alice	1001	project
bob	1002	project
charlie	1003	guest
diana	1004	admin
EOF

cat > scores.txt <<'EOF'
Alice 92
Bob 7
Charlie 81
Diana 100
EOF

cat > repeated.txt <<'EOF'
banana
apple
banana
orange
apple
apple
EOF
```

---

# Application Day 1: Apply Text Processing to Earlier Chapters

Suggested day: Saturday

Purpose:

```text
Use Chapter 20 commands to review Chapters 1-19.
```

This day has no new commands. It uses text processing as an investigation tool.

## Feynman analogy

Earlier chapters taught him how to walk around a workshop. Chapter 20 gives him measuring tools.

Now he uses those measuring tools on files, permissions, processes, and command output.

## Task 1: Process file listings

```bash
cd ~
ls -l
ls -l | head
ls -l | tr -s ' '
ls -l | tr -s ' ' | cut -d ' ' -f1,3,5,9
```

Question: What columns did you extract?

Expected idea: permissions, owner, size, name.

## Task 2: Review permissions with text tools

```bash
cd ~/tlcl-ch20-week
find . -maxdepth 2 -type f -ls | head
find . -maxdepth 2 -type f -ls | tr -s ' ' | cut -d ' ' -f4,6,11
```

Important: fields may vary. Inspect first. Do not trust a pipeline blindly.

## Task 3: Process `/etc/passwd`

```bash
cut -d ':' -f1 /etc/passwd | head
cut -d ':' -f7 /etc/passwd | sort | uniq -c
```

Question: What does the second command summarize?

Expected: It counts which login shells appear in `/etc/passwd`.

## Task 4: Process command history

```bash
history | tail -n 30
history | tr -s ' ' | cut -d ' ' -f3 | sort | uniq -c | sort -n
```

Warning: history formatting can vary. Inspect intermediate output before trusting it.

## Task 5: Process process output

```bash
ps -eo user,comm | head
ps -eo user,comm | tail -n +2 | sort | uniq -c | sort -n
```

Question: What does this summarize?

## Reflection

Answer in writing:

1. Which command helped select rows?
2. Which command helped select columns?
3. Which command helped count repeated values?
4. Which command helped clean spacing?
5. Which result looked suspicious and needed inspection?

## Retention rule

Do not trust a pipeline because it runs. Trust it only after checking intermediate stages.
