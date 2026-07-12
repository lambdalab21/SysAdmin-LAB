#find  #Book/The-Linux-Command-Line 
# Effective Shell Chapter 3: Finding Files

This guide is designed to reinforce Author/Shotts, *The Linux Command Line*, Chapter 17.

Main bridge:

```text
Author/Shotts Chapter 17 teaches the mechanics.
Effective Shell Chapter 3 helps the structure click.
```

Core habit:

```text
Do not memorize find commands as magic spells.
Translate an English search question into a find expression.
```

The command shape to keep using:

```text
find [options] [starting point...] [expression]
```

For disciplined learning, rewrite that as:

```text
find WHERE TESTS ACTION
```

Before every command, answer:

```text
1. Where am I searching?
2. What kind of item do I want?
3. What condition must match?
4. What action will happen?
5. Is the command safe?
6. How will I verify the result?
```

One-time lab setup:

```bash
mkdir -p ~/effective-shell-ch03-lab/{logs,logs/apm-logs,websites/simple,programs/web-server,scripts,text,tmp,archive}
cd ~/effective-shell-ch03-lab

touch logs/web-server-logs.txt
touch logs/apm-logs/apm00.logs
touch logs/apm-logs/apm01.logs
touch logs/apm-logs/apm02.logs
touch logs/apm-logs/apm03.logs
touch logs/apm-logs/apm04.logs
touch logs/apm-logs/apm05.logs

touch websites/simple/index.html
touch websites/simple/styles.css
touch websites/simple/code.js

touch programs/web-server/web-server.js
touch scripts/show-info.sh
touch text/simpsons-characters.txt

touch tmp/delete-me.tmp
touch tmp/empty.tmp
touch archive/old-notes.txt

echo '#!/usr/bin/env bash' > scripts/show-info.sh
echo 'echo info' >> scripts/show-info.sh
chmod 755 scripts/show-info.sh

touch -d "2 days ago" archive/old-notes.txt
```

Verify:

```bash
find ~/effective-shell-ch03-lab -maxdepth 3 -print
```

---
# Session 2: Reinforcement Lab

## Purpose

This lab solidifies Author/Shotts Chapter 17 using Effective Shell Chapter 3’s workflow.

The goal is not speed.

The goal is disciplined translation:

```text
English question
→ find WHERE TESTS ACTION
→ prediction
→ command
→ verification
```

---

# Lab rule

For every task, fill this out before running the command:

```text
English question:
WHERE:
TESTS:
ACTION:
Risk:
Prediction:
```

After running:

```text
Actual result:
Was prediction correct?
What did I misunderstand?
```

---

# Task 1: Find all log-related names

English question:

```text
Find anything whose name contains log.
```

Command:

```bash
cd ~/effective-shell-ch03-lab
find . -name "*log*"
```

Now make it stricter:

```bash
find . -type f -name "*log*"
find . -type d -name "*log*"
```

Questions:

1. Which command found both files and directories?
2. Which command found only files?
3. Which command found only directories?
4. Why does `-type` matter?

---

# Task 2: Name versus path

English question:

```text
Find everything inside the apm-logs directory.
```

Bad first attempt:

```bash
find . -name "*apm-logs*"
```

Better:

```bash
find . -path "*apm-logs*"
find . -type f -path "*apm-logs*"
```

Explain:

```text
Why did -name not find the files inside the directory?
Why did -path find them?
```

---

# Task 3: Find web files but exclude programs

English question:

```text
Find .js or .html files, but exclude anything inside programs.
```

Build it in stages:

```bash
find . -name "*.js"
find . -name "*.html"
find . \( -name "*.js" -or -name "*.html" \)
find . \( -name "*.js" -or -name "*.html" \) -not -path "*programs*"
find . \( -name "*.js" -or -name "*.html" \) -not -path "*programs*" -type f
```

Discipline question:

```text
Which stage first excluded programs?
Which stage ensured the result was ordinary files?
```

---

# Task 4: Case-insensitive search

English question:

```text
Find HTML files even if their extension has mixed case.
```

Command:

```bash
find . -type f -iname "*.html"
```

Now compare:

```bash
find . -type f -name "*.html"
find . -type f -iname "*.html"
```

Explain:

```text
What did -iname find that -name missed?
```

---

# Task 5: Recently changed files

English question:

```text
Find ordinary files changed in the last two days.
```

Command:

```bash
find . -type f -mtime -2 -ls
```

Now compare:

```bash
find . -type f -mtime +1 -ls
```

Explain:

```text
What does -2 mean?
What does +1 mean?
Why is -ls useful here?
```

---

# Task 6: Empty files

English question:

```text
Find empty ordinary files.
```

Command:

```bash
find . -type f -empty -ls
```

Explain:

```text
Why does -type f matter?
What might happen if we searched for -empty without -type f?
```

---

# Task 7: Preview before deletion

English question:

```text
Delete temporary .tmp files safely.
```

Step 1: Preview paths.

```bash
find . -type f -name "*.tmp" -print
```

Step 2: Preview details.

```bash
find . -type f -name "*.tmp" -ls
```

Step 3: Use confirmation, not blind delete.

```bash
find . -type f -name "*.tmp" -ok rm {} \;
```

Recreate if needed:

```bash
touch tmp/delete-me.tmp tmp/empty.tmp
```

Explain:

```text
Why is -ok safer than -delete?
Why is -ls better than deleting immediately?
```

---

# Task 8: `-exec` as a readable action

English question:

```text
Count words in every text-like file.
```

Command:

```bash
find . \( -name "*.txt" -or -name "*.md" \) -type f -exec wc -w {} \;
```

Explain:

```text
What does {} become?
Why does \; appear?
What would be safer or more flexible later with xargs?
```

---

# Task 9: Connect back to Author/Shotts Chapter 17

For each Author/Shotts concept, write one Effective Shell example:

| Author/Shotts concept | Effective Shell-style example |
|---|---|
| `-type` | |
| `-name` | |
| `-iname` | |
| `-path` | |
| grouped OR | |
| `-not` | |
| `-mtime` | |
| `-exec` | |
| safe deletion | |

---

# Lab finish standard

He is done only if he can explain this command without help:

```bash
find . \( -name "*.js" -or -name "*.html" \) -and -not -path "*programs*" -type f -ls
```

Required explanation:

```text
WHERE:
GROUPED TEST:
EXCLUSION TEST:
TYPE TEST:
ACTION:
WHY QUOTES:
WHY ESCAPED PARENTHESES:
RISK:
```
