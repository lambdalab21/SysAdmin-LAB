#Book/The-Linux-Command-Line  #Author/Shotts 
#process 
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

# Application Day 2: Apply Text Processing to Logs, Processes, and Networking

Suggested day: Sunday

Purpose:

```text
Use Chapter 20 tools with realistic sysadmin-style output.
```

## Feynman analogy

A sysadmin receives messy evidence:

```text
logs
process lists
network socket lists
command output
```

Text processing turns messy evidence into smaller, clearer clues.

## Task 1: Analyze a small log file

```bash
cd ~/tlcl-ch20-week
cat sample.log
```

### Question A: How many ERROR lines?

```bash
grep ERROR sample.log | wc -l
```

### Question B: Which hosts appear?

```bash
cut -d ' ' -f3 sample.log | sort | uniq
```

### Question C: How many messages per level?

```bash
cut -d ' ' -f1 sample.log | sort | uniq -c
```

### Question D: Which users had failed login attempts?

```bash
grep 'failed-login' sample.log | cut -d ' ' -f5 | sort | uniq
```

## Task 2: Analyze process output

```bash
ps -eo user,pid,comm | head
ps -eo comm | tail -n +2 | sort | uniq -c | sort -n | tail
```

Question: Which command names appear most often?

## Task 3: Analyze listening ports

```bash
ss -tuln
ss -tuln | head
ss -tuln | tr -s ' '
ss -tuln | grep tcp
```

Important: `ss` output differs by system. Inspect before cutting fields.

## Task 4: Create a tiny report

```bash
{
  echo "Log summary"
  echo "==========="
  echo
  printf 'Error lines: '
  grep ERROR sample.log | wc -l
  echo
  echo "Messages by level:"
  cut -d ' ' -f1 sample.log | sort | uniq -c
} > log-summary.txt
cat log-summary.txt
```

## Final weekly self-test

Without notes, write commands to answer:

1. Count unique lines in `repeated.txt`.
2. Extract usernames from `/etc/passwd`.
3. Convert lowercase text to uppercase.
4. Replace `app01` with `web01` in output.
5. Count message levels in `sample.log`.
6. Extract users from `failed-login` lines.
7. Make a two-column formatted header with `printf`.

Expected command patterns:

```bash
sort repeated.txt | uniq | wc -l
cut -d ':' -f1 /etc/passwd
echo 'hello' | tr '[:lower:]' '[:upper:]'
sed 's/app01/web01/g' sample.log
cut -d ' ' -f1 sample.log | sort | uniq -c
grep 'failed-login' sample.log | cut -d ' ' -f5 | sort | uniq
printf '%-10s %s
' Name Score
```

## End-of-week standard

He is ready to move on if he can build a pipeline one stage at a time, explain each stage, inspect intermediate output, and avoid treating command examples as magic spells.
