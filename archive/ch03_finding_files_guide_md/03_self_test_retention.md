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
# Session 3: Self-Test and Retention

## Purpose

This file checks whether he can use Effective Shell Chapter 3 to strengthen Author/Shotts Chapter 17.

No notes at first.

If he cannot answer, he should return to the reading guide and redo the specific section.

---

# Part 1: Explain the mental model

Answer without notes:

1. What is the basic structure of `find`?
2. Why does the starting point usually come before `-name` or `-type`?
3. Why are `-name`, `-type`, `-or`, and `-not` better understood as parts of a search expression?
4. What is the difference between a command option and a search expression?
5. Why does this make `find` feel strange at first?

Expected idea:

```text
find [options] [starting point...] [expression]
```

For practical use:

```text
find WHERE TESTS ACTION
```

---

# Part 2: Translate English into commands

Write commands for each task.

## 1. Find all ordinary `.logs` files.

Expected pattern:

```bash
find . -type f -name "*.logs"
```

## 2. Find anything with `log` in its name.

Expected pattern:

```bash
find . -name "*log*"
```

## 3. Find files inside a path containing `apm-logs`.

Expected pattern:

```bash
find . -type f -path "*apm-logs*"
```

## 4. Find `.js` or `.html` files.

Expected pattern:

```bash
find . \( -name "*.js" -or -name "*.html" \) -type f
```

## 5. Find `.js` or `.html` files but exclude `programs`.

Expected pattern:

```bash
find . \( -name "*.js" -or -name "*.html" \) -type f -not -path "*programs*"
```

## 6. Find HTML files regardless of case.

Expected pattern:

```bash
find . -type f -iname "*.html"
```

## 7. Find recently modified files.

Expected pattern:

```bash
find . -type f -mtime -2 -ls
```

## 8. Find empty directories.

Expected pattern:

```bash
find . -type d -empty -ls
```

## 9. Preview `.tmp` files before deleting.

Expected pattern:

```bash
find . -type f -name "*.tmp" -ls
```

## 10. Remove `.tmp` files with confirmation.

Expected pattern:

```bash
find . -type f -name "*.tmp" -ok rm {} \;
```

---

# Part 3: Diagnose bad commands

Explain what is wrong or risky.

## Bad command 1

```bash
find . -name *.log
```

Expected issue:

```text
The shell may expand *.log before find sees it. Quote the pattern.
```

Better:

```bash
find . -name "*.log"
```

---

## Bad command 2

```bash
find . -name "*apm-logs*"
```

when the goal is:

```text
Find files inside the apm-logs directory.
```

Expected issue:

```text
-name checks only the final filename. Use -path for the whole path.
```

Better:

```bash
find . -type f -path "*apm-logs*"
```

---

## Bad command 3

```bash
find ~ -name "*.tmp" -delete
```

Expected issue:

```text
The search is broad and deletion happens without preview or confirmation.
```

Better learning pattern:

```bash
find ~/some-limited-lab -type f -name "*.tmp" -ls
find ~/some-limited-lab -type f -name "*.tmp" -ok rm {} \;
```

---

## Bad command 4

```bash
find . -name "*.js" -or -name "*.html" -not -path "*programs*"
```

Expected issue:

```text
Without grouping, the logic may not match the intended English rule. Group OR conditions.
```

Better:

```bash
find . \( -name "*.js" -or -name "*.html" \) -not -path "*programs*"
```

---

# Part 4: Feynman explain-back

Explain this to a younger student:

```bash
find . \( -name "*.js" -or -name "*.html" \) -and -not -path "*programs*" -type f -ls
```

Use simple language:

```text
Start searching...
The parentheses mean...
The OR means...
The NOT means...
The type test means...
The action means...
The quotes are needed because...
The escaped parentheses are needed because...
```

If he cannot explain it simply, he does not understand it yet.

---

# Part 5: Retention schedule

Repeat this short check:

```text
1 day later
1 week later
1 month later
```

Commands to recall:

```bash
find . -type f
find . -type d
find . -name "*.logs"
find . -path "*apm-logs*"
find . -type f -iname "*.html"
find . \( -name "*.js" -or -name "*.html" \) -type f
find . -type f -mtime -2 -ls
find . -type f -name "*.tmp" -ls
```

For each command, identify:

```text
WHERE:
TESTS:
ACTION:
RISK:
```

---

# Final standard

He has solidified Author/Shotts Chapter 17 with Effective Shell Chapter 3 only if he can say:

```text
I understand find as a search expression.
I know the difference between name and path.
I quote wildcard patterns.
I group OR expressions.
I preview before deleting.
I can explain a find command in plain English before running it.
```
