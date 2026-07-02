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

# Unit 5: Applied Text-Processing Pipelines

Suggested day: Friday

## Read these Chapter 20 sections

Reread examples that combine commands into pipelines. Focus on:

```text
sort
uniq
cut
paste
join
tr
sed
printf
```

Also reuse older tools:

```text
grep
wc
head
tail
less
redirection
pipes
```

## Feynman analogy before reading

A pipeline is an assembly line. Each worker does one simple job.

```text
Worker 1 selects relevant lines.
Worker 2 selects useful columns.
Worker 3 sorts values.
Worker 4 removes duplicates.
Worker 5 counts the result.
```

A good pipeline is built one worker at a time.

## Before touching the keyboard

Answer:

1. Why should you build pipelines one stage at a time?
2. What command helps select lines?
3. What command helps select fields?
4. What command removes adjacent duplicates?
5. Why should you inspect intermediate output?

## Practice

```bash
cd ~/tlcl-ch20-week
cp sample.log unit5/
cd unit5
cat sample.log
```

### Drill 1: Count error lines

```bash
cat sample.log
grep ERROR sample.log
grep ERROR sample.log | wc -l
```

### Drill 2: Extract failed-login users

```bash
grep 'failed-login' sample.log
grep 'failed-login' sample.log | cut -d ' ' -f5
```

Question: Which field contains the username?

Expected: Field 5 in this sample data.

### Drill 3: Count messages by level

```bash
cut -d ' ' -f1 sample.log
cut -d ' ' -f1 sample.log | sort
cut -d ' ' -f1 sample.log | sort | uniq -c
```

### Drill 4: Count messages by host

```bash
cut -d ' ' -f3 sample.log
cut -d ' ' -f3 sample.log | sort
cut -d ' ' -f3 sample.log | sort | uniq -c
```

### Drill 5: Create a rough report

```bash
{
  printf '%-10s %s
' Level Count
  cut -d ' ' -f1 sample.log | sort | uniq -c
} > report.txt
cat report.txt
```

### Drill 6: Replace hostnames in output only

```bash
sed 's/app01/web01/g' sample.log
cat sample.log
```

Say aloud:

```text
sed changed the output, not the original file.
```

## Checkpoint

1. Why build pipelines one stage at a time?
2. How do `grep`, `cut`, `sort`, and `uniq` work together?
3. How do you count message levels?
4. How do you extract usernames from matching log lines?
5. How do you redirect a report to a file?
