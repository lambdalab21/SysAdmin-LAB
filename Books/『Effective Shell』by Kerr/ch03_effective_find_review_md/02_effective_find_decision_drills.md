# Effective Shell Chapter 3: Finding Files — Review After Shotts Chapter 17

Purpose:

```text
Shotts Chapter 17 taught the tools.
Effective Shell Chapter 3 should teach better judgment.
```

Do not treat this as more `find` typing practice. Treat it as learning how to think like someone who can ask precise questions about the filesystem.

Core habit:

```text
English question → search scope → tests → action → verification
```

Use the Shotts pattern:

```text
find WHERE TESTS ACTION
```

Use the Effective Shell mental model:

```text
find [starting point...] [expression]
```

Before pressing Enter, fill this out:

```text
Question I am answering:
WHERE I am searching:
TESTS I am applying:
ACTION I am taking:
Possible false positives:
Possible false negatives:
How I will verify:
Risk level: low / medium / high
```

One-time lab setup:

```bash
mkdir -p ~/es-ch03-find-review/{logs,logs/apm-logs,websites/simple,programs/web-server,scripts,text,tmp,archive,backup}
cd ~/es-ch03-find-review

touch logs/web-server-logs.txt
touch logs/apm-logs/apm00.logs
touch logs/apm-logs/apm01.logs
touch logs/apm-logs/apm02.logs
touch logs/apm-logs/apm03.logs

touch websites/simple/index.html
touch websites/simple/styles.css
touch websites/simple/code.js
touch websites/simple/README.HTML

touch programs/web-server/web-server.js
touch scripts/show-info.sh
touch text/simpsons-characters.txt
touch tmp/delete-me.tmp
touch tmp/empty.tmp
touch archive/old-notes.txt

echo '#!/usr/bin/env bash' > scripts/show-info.sh
echo 'echo info' >> scripts/show-info.sh
chmod 755 scripts/show-info.sh

touch -d "3 days ago" archive/old-notes.txt
touch -d "1 hour ago" logs/web-server-logs.txt
```

Verify the lab tree:

```bash
find ~/es-ch03-find-review -maxdepth 3 -print
```

---
# Session 2: Effective `find` Decision Drills

## Purpose

This session trains decision-making.

A mindless learner asks:

```text
Which command did the book show?
```

An effective learner asks:

```text
What question am I answering?
Which part of `find` answers that question?
```

---

# Decision drill format

For every task, write this before running the command:

```text
Question:
Should I use -name or -path?
Should I use -type?
Do I need AND, OR, or NOT?
Should the pattern be quoted?
What false positives might appear?
```

---

# Drill 1: “Find log things” is too vague

Vague request:

```text
Find log things.
```

Better questions:

```text
Find anything with log in the name.
Find only files with log in the name.
Find only directories with log in the name.
Find files inside a log directory.
```

Commands:

```bash
cd ~/es-ch03-find-review
find . -name '*log*'
find . -type f -name '*log*'
find . -type d -name '*log*'
find . -type f -path '*logs*'
```

Explain:

```text
How did each question change the command?
Which command was broadest?
Which was most precise?
```

---

# Drill 2: Name or path?

Task:

```text
Find files inside the apm-logs directory.
```

First, predict why this is incomplete:

```bash
find . -name '*apm-logs*'
```

Now run:

```bash
find . -name '*apm-logs*'
find . -path '*apm-logs*'
find . -type f -path '*apm-logs*'
```

Explain:

```text
`-name` answered a different question than I intended.
The intended question was about location, so `-path` was better.
```

---

# Drill 3: Build OR slowly

Task:

```text
Find web source files ending in .html or .js.
```

Do not type the final command first.

Stage 1:

```bash
find . -type f -name '*.html'
```

Stage 2:

```bash
find . -type f -name '*.js'
```

Stage 3:

```bash
find . \( -name '*.html' -or -name '*.js' \) -type f
```

Stage 4: include mixed-case HTML too.

```bash
find . \( -iname '*.html' -or -name '*.js' \) -type f
```

Explain:

```text
Why did I switch from -name to -iname for HTML?
Why did I keep -name for JS?
```

---

# Drill 4: Exclude a subtree

Task:

```text
Find .js files, but exclude programs.
```

Run:

```bash
find . -type f -name '*.js'
find . -type f -name '*.js' -not -path '*programs*'
```

Explain:

```text
The first command found all JS files.
The second command excluded paths containing programs.
```

Discipline question:

```text
Is excluding paths by string enough for serious work, or should I inspect the output carefully?
```

Expected answer:

```text
Inspect carefully. Path patterns can be broader or narrower than expected.
```

---

# Drill 5: Add time as evidence

Task:

```text
Find files changed recently.
```

Commands:

```bash
find . -type f -mtime -1 -ls
find . -type f -mtime +2 -ls
```

Explain:

```text
-mtime -1 means modified within the last day.
-mtime +2 means older than two days.
```

Effective question:

```text
What problem would this help troubleshoot?
```

Expected idea:

```text
It helps find files changed around the time a problem started.
```

---

# Drill 6: Choose the safer display action

Task:

```text
Inspect temporary files before removing anything.
```

Commands:

```bash
find . -type f -name '*.tmp' -print
find . -type f -name '*.tmp' -ls
```

Explain:

```text
-print gives paths.
-ls gives details.
For deletion decisions, details are safer.
```

---

# Drill 7: The effective-user rewrite

Rewrite each vague command into a better command.

## Vague command A

```bash
find . -name '*log*'
```

When the real goal is ordinary log files.

Better:

```bash
find . -type f -name '*log*'
```

## Vague command B

```bash
find ~ -name '*.tmp'
```

When the real goal is tmp files only in this lab.

Better:

```bash
find ~/es-ch03-find-review -type f -name '*.tmp' -ls
```

## Vague command C

```bash
find . -name '*.html' -or -name '*.js'
```

When the real goal is ordinary files ending in `.html` or `.js`.

Better:

```bash
find . \( -name '*.html' -or -name '*.js' \) -type f
```

---

# End standard

He is effective if he can say:

```text
I changed the command because I changed the question.
```

Not:

```text
I changed the command because the book did.
```
