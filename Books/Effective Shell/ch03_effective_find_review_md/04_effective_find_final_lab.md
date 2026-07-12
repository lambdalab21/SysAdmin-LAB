#Book/Effective-Shell #Author/Kerr 
#drill #find #search 
# Effective Shell Chapter 3: Finding Files — Review After Author/Shotts Chapter 17

Purpose:

```text
Author/Shotts Chapter 17 taught the tools.
Effective Shell Chapter 3 should teach better judgment.
```

Do not treat this as more `find` typing practice. Treat it as learning how to think like someone who can ask precise questions about the filesystem.

Core habit:

```text
English question → search scope → tests → action → verification
```

Use the Author/Shotts pattern:

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
# Session 4: Final Lab — Becoming Effective with `find`

## Purpose

This lab tests whether he has moved beyond Author/Shotts-style command familiarity into Effective Shell-style judgment.

No notes at first.

If stuck, write the English question again more clearly.

---

# Part 1: Translate requests into precise searches

For each request, write:

```text
Question:
WHERE:
TESTS:
ACTION:
False positives:
False negatives:
Verification:
```

Then write the command.

---

## Request 1

```text
Find all ordinary log files in the lab.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -name '*log*' -ls
```

---

## Request 2

```text
Find files inside the apm-logs directory.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -path '*apm-logs*' -ls
```

---

## Request 3

```text
Find `.html` files, including mixed-case extensions.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -iname '*.html' -ls
```

---

## Request 4

```text
Find `.js` or `.html` files, but exclude programs.
```

Expected pattern:

```bash
find ~/es-ch03-find-review \( -name '*.js' -or -iname '*.html' \) -type f -not -path '*programs*' -ls
```

---

## Request 5

```text
Find files changed within the last day.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -mtime -1 -ls
```

---

## Request 6

```text
Find executable ordinary files.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -executable -ls
```

---

## Request 7

```text
Preview temporary files that might be deleted.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -name '*.tmp' -ls
```

---

## Request 8

```text
Remove temporary files, but only with confirmation.
```

Expected pattern:

```bash
find ~/es-ch03-find-review -type f -name '*.tmp' -ok rm {} \;
```

---

# Part 2: Explain complex commands

Explain this command as if teaching a younger student:

```bash
find ~/es-ch03-find-review \( -name '*.js' -or -iname '*.html' \) -type f -not -path '*programs*' -ls
```

Required explanation:

```text
Search root:
Grouped condition:
Why -or:
Why -iname for html:
Why -type f:
What is excluded:
What action is used:
What risks remain:
```

---

# Part 3: Diagnose mistakes

## Mistake 1

```bash
find . -name *.html
```

Diagnosis:

```text
Wildcard pattern is unquoted. The shell may expand it first.
```

Better:

```bash
find . -name '*.html'
```

---

## Mistake 2

```bash
find . -name '*apm-logs*'
```

when the goal is files inside that directory.

Diagnosis:

```text
-name checks only the final name. Use -path for location in the tree.
```

Better:

```bash
find . -type f -path '*apm-logs*'
```

---

## Mistake 3

```bash
find . -name '*.js' -or -name '*.html' -type f
```

Diagnosis:

```text
The OR expression is not grouped. The logic may not mean what was intended.
```

Better:

```bash
find . \( -name '*.js' -or -name '*.html' \) -type f
```

---

## Mistake 4

```bash
find ~ -name '*.tmp' -delete
```

Diagnosis:

```text
Search root is too broad and deletion happens without preview.
```

Better:

```bash
find ~/es-ch03-find-review -type f -name '*.tmp' -ls
find ~/es-ch03-find-review -type f -name '*.tmp' -ok rm {} \;
```

---

# Part 4: Retention check

Repeat after:

```text
1 day
1 week
1 month
```

Commands to recall:

```bash
find . -type f
find . -type d
find . -type f -name '*log*'
find . -type f -path '*apm-logs*'
find . -type f -iname '*.html'
find . \( -name '*.js' -or -iname '*.html' \) -type f
find . -type f -mtime -1 -ls
find . -type f -name '*.tmp' -ls
find . -type f -name '*.tmp' -ok rm {} \;
```

For each command, answer:

```text
What question does it answer?
What could it match accidentally?
What could it miss?
Is it safe?
```

---

# End standard

He has learned Effective Shell Chapter 3 effectively if he can say:

```text
I do not start with a command.
I start with a question.
I choose name, path, type, logic, and action intentionally.
I preview before acting.
I can explain the command before running it.
```
