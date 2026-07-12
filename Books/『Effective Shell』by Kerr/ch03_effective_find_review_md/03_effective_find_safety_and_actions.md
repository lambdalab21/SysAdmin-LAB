#Book/Effective-Shell #Author/Kerr
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
# Session 3: Safety, Actions, and Verification

## Purpose

This session focuses on the dangerous side of `find`.

The chapter is not only about locating files. `find` can also run actions on matches.

That means:

```text
A bad search expression can become a bad filesystem change.
```

---

# Feynman analogy

Searching is looking around the room.

Actions are touching things in the room.

Deleting is throwing things away.

A careful person does not throw things away while blindfolded.

So the safe sequence is:

```text
search → inspect → confirm → act → verify
```

---

# Action ladder

Use this ladder before any risky action:

```text
Level 1: -print      show paths
Level 2: -ls         show details
Level 3: -ok CMD     ask before command
Level 4: -exec CMD   run command without asking
Level 5: -delete     delete matches directly
```

For learning, stay mostly at levels 1–3.

---

# Drill 1: Preview temporary files

Predict first:

```text
Which files should match *.tmp?
Are they all safe to remove?
Am I in the right directory?
```

Commands:

```bash
cd ~/es-ch03-find-review
find . -type f -name '*.tmp' -print
find . -type f -name '*.tmp' -ls
```

Explain:

```text
The search is limited to this lab.
The files are ordinary files.
The names end in .tmp.
No action has changed the filesystem yet.
```

---

# Drill 2: Use `-ok` before `-exec`

Run:

```bash
find . -type f -name '*.tmp' -ok ls -l {} \;
```

Answer the prompts carefully.

Explain:

```text
-ok asks for confirmation before running the command on each match.
{} becomes the matched pathname.
\; marks the end of the command.
```

---

# Drill 3: Confirm before removal

Step 1: preview.

```bash
find . -type f -name '*.tmp' -ls
```

Step 2: remove with confirmation.

```bash
find . -type f -name '*.tmp' -ok rm {} \;
```

Step 3: verify.

```bash
find . -type f -name '*.tmp' -print
```

Recreate the practice files:

```bash
touch tmp/delete-me.tmp tmp/empty.tmp
```

Explain:

```text
I previewed first.
I used confirmation.
I verified after acting.
I recreated the lab files.
```

---

# Drill 4: Why not `-delete` first?

Compare:

```bash
find . -type f -name '*.tmp' -ls
```

with:

```bash
find . -type f -name '*.tmp' -delete
```

Do not run `-delete` unless you intentionally want to remove the practice files.

Question:

```text
What is missing from the direct -delete command?
```

Expected answer:

```text
It has no confirmation step and no visible preview.
```

---

# Drill 5: High-risk command diagnosis

Explain why each command is risky.

## Risky command A

```bash
find ~ -name '*.tmp' -delete
```

Problem:

```text
The search scope is too broad and deletion is immediate.
```

Better:

```bash
find ~/es-ch03-find-review -type f -name '*.tmp' -ls
find ~/es-ch03-find-review -type f -name '*.tmp' -ok rm {} \;
```

## Risky command B

```bash
find . -name '*log*' -exec rm {} \;
```

Problem:

```text
It may remove directories or files that merely contain log in the name.
It has no type test and no confirmation.
```

Better:

```bash
find . -type f -name '*log*' -ls
```

Only after inspection should removal even be considered.

## Risky command C

```bash
find / -name '*.conf' -exec cat {} \;
```

Problem:

```text
The search starts at root and may produce permission errors, huge output, and unnecessary system-wide traversal.
```

Better:

```bash
find /etc -type f -name '*.conf' -print 2>/dev/null | head
```

---

# Drill 6: Verification thinking

For each action, choose a verification command.

| Action | Verification idea |
|---|---|
| Deleted `.tmp` files | `find . -type f -name '*.tmp' -print` |
| Changed permissions | `find . -type f -perm ... -ls` |
| Copied files | `find destination -type f -print` |
| Found recent files | `stat file` or `find ... -ls` |

Question:

```text
Why is “the command had no error” not enough verification?
```

Expected answer:

```text
A command can run successfully but still do the wrong thing.
```

---

# Final safety checklist

Before using `-exec`, `-ok`, `-delete`, or `xargs rm`, say:

```text
1. I know the exact search root.
2. I know whether I mean files or directories.
3. I previewed matches with -print or -ls.
4. I understand what {} becomes.
5. I know whether confirmation will happen.
6. I know how to verify afterward.
```
