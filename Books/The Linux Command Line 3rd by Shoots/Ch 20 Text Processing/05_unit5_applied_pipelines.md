# The Linux Command Line, Chapter 20: Text Processing

Use these files as a one-week plan. Each day has a reading target, a Feynman-style analogy, questions, and command practice.

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

## Before touching the keyboard

Answer:

1. Why should you build pipelines one stage at a time? To verity each part works right before adding the next, making debugging easier. 
2. What command helps select lines? grep
3. What command helps select fields? cut
4. What command removes adjacent duplicates? uniq
5. Why should you inspect intermediate output? To catch mistakes early and understand what each stage produces. 

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
## Checkpoint

1. Why build pipelines one stage at a time? Easier to test, debug, and understand each transformation. 
2. How do `grep`, `cut`, `sort`, and `uniq` work together? grep filters lines, cut extracts fields, sort orders them, and uniq -c counts unique items. 
3. How do you count message levels? cuts -d ' ' -f1 sample.log | sort | uniq -c
4. How do you extract usernames from matching log lines? grep 'failed-login' sample.log | cut -d ' ' -f5
5. How do you redirect a report to a file? use > report.txt. 
