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
# Session 1: Active Reading Guide

## Purpose

This reading guide uses *Effective Shell* Chapter 3 to make Author/Shotts Chapter 17 stick.

Author/Shotts gave him many useful `find` tests:

```text
-type
-name
-iname
-size
-mtime
-mmin
-user
-perm
-executable
-maxdepth
-mindepth
-exec
-delete
xargs
locate
```

Effective Shell adds an important mental model:

```text
find has a starting point and a search expression.
```

That is why the command often feels strange.

---

# Before reading: Feynman analogy

Imagine you are sending a careful helper into a warehouse.

You cannot just say:

```text
Find stuff.
```

You must say:

```text
Start in this aisle.
Look only for boxes, not shelves.
The label must end in .log.
When you find them, show me the details.
```

That becomes:

```bash
find . -type f -name "*.log" -ls
```

The helper needs four things:

```text
starting place
item type
matching rule
action
```

---

# Reading block 1: Introducing `find`

Read the section that introduces the `find` command.

## Stop and answer

1. What does plain `find` or `find .` show?
2. Does it show only files?
3. Does it descend into child directories?
4. Why is `find .` clearer than just `find` for a beginner?

## Practice

```bash
cd ~/effective-shell-ch03-lab

find .
find . | head
find . | wc -l
```

## Explain-back

Say aloud:

```text
find . starts in the current directory and walks down through the tree.
With no tests, it prints everything it finds.
```

---

# Reading block 2: Searching for files or folders only

Read the section on `-type`.

## Stop and answer

1. What does `-type f` mean?
2. What does `-type d` mean?
3. Why should he often include `-type f` before using `-name`?
4. What false matches could appear if he does not use `-type`?

## Practice

```bash
find . -type f
find . -type d
find . -type f | wc -l
find . -type d | wc -l
```

## Explain-back

```text
-type f means ordinary files.
-type d means directories.
Adding -type makes my search more precise.
```

---

# Reading block 3: Searching by name

Read the section on `-name`, including the note about quotes.

## Feynman analogy

`-name` checks the label on the item, not the whole address.

If a file is here:

```text
./logs/apm-logs/apm00.logs
```

The name is:

```text
apm00.logs
```

The path is:

```text
./logs/apm-logs/apm00.logs
```

That distinction matters.

## Stop and answer

1. What does `-name "apm-logs"` match?
2. Why does it not match every file inside `apm-logs`?
3. Why do we quote `"*log*"`?
4. What could the shell do if we forget quotes?

## Practice

```bash
find . -name "apm-logs"
find . -name "*apm-logs*"
find . -name "*log*"
find . -name "*.logs"
find . -type f -name "*.logs"
```

## Shell-interference drill

Run carefully:

```bash
printf 'Quoted pattern:
'
find . -name "*log*" | sort

printf 'Shell expansion example:
'
echo *log*
```

Do **not** make a habit of unquoted patterns with `find`.

## Explain-back

```text
-name tests the final filename.
-path tests the full path.
Quoted patterns prevent the shell from expanding wildcards before find receives them.
```

---

# Reading block 4: Searching by path

Read the section on `-path`.

## Stop and answer

1. What is the difference between `-name` and `-path`?
2. Why does `-path "*apm-logs*"` match files inside the `apm-logs` directory?
3. When is `-path` more useful than `-name`?

## Practice

```bash
find . -name "*apm-logs*"
find . -path "*apm-logs*"
find . -type f -path "*apm-logs*"
find . -type f -path "*websites/simple*"
```

## Explain-back

```text
-name checks only the final name.
-path checks the whole path.
```

---

# Reading block 5: AND and OR

Read the section on combining searches with AND and OR.

## Feynman analogy

A search expression is like a filter at a security gate.

```text
-type f -name "*.logs"
```

means:

```text
must be a file AND must have a name ending in .logs
```

OR means either condition may pass.

## Stop and answer

1. What does implicit AND mean?
2. What does `-or` mean?
3. Why can OR searches become confusing without grouping?

## Practice

```bash
find . -type f -name "*.logs"
find . -name "*.js" -or -name "*.html"
find . \( -name "*.js" -or -name "*.html" \) -type f
```

## Explain-back

For each command, identify:

```text
WHERE:
LEFT TEST:
RIGHT TEST:
LOGIC:
ACTION:
```

---

# Reading block 6: Case-insensitive searches

Read the section on `-iname` and `-ipath`.

## Practice setup

```bash
touch websites/simple/README.HTML
touch logs/APM06.LOGS
```

## Practice

```bash
find . -name "*.html"
find . -iname "*.html"
find . -name "*.logs"
find . -iname "*.logs"
find . -ipath "*APM-LOGS*"
```

## Stop and answer

1. When is case-insensitive search useful?
2. When could case-insensitive search hide a mistake?
3. Should Linux filenames generally be treated as case-sensitive?

---

# Reading block 7: Grouping and NOT

Read the section on grouping and `-not`.

## Feynman analogy

Grouping is like parentheses in math.

```text
A OR B AND C
```

can be confusing.

```text
(A OR B) AND C
```

is clearer.

## Practice

```bash
find . \( -name "*.js" -or -name "*.html" \) -type f
find . \( -name "*.js" -or -name "*.html" \) -and -not -path "*programs*"
find . -type f -not -path "*archive*"
```

## Explain-back

Explain this command:

```bash
find . \( -name "*.js" -or -name "*.html" \) -and -not -path "*programs*"
```

Use this form:

```text
Start in...
Match names that...
Group means...
Exclude paths that...
The result should include...
The result should exclude...
```

---

# Reading block 8: Why the parameters feel weird

Read the section explaining why `find` parameters feel unusual.

## Main idea

Do not think of everything after `find` as normal options.

Think:

```text
find [options] [starting point...] [expression]
```

Effective Shell’s important lesson is that `-name`, `-type`, `-or`, `-not`, and actions are parts of a search expression.

## Stop and answer

1. Why does the starting point come before the expression?
2. What is the search expression?
3. Why is this mental model useful?
4. How does this connect to Author/Shotts Chapter 17?

---

# Reading block 9: Actions and safety

Read the sections on:

```text
-print
-delete
-exec
-ok
```

## Stop and answer

1. Why is `-print` often implicit?
2. Why is `-delete` risky?
3. What does `{}` mean in `-exec`?
4. Why does `\;` need escaping?
5. Why is `-ok` safer than `-exec` for learning?

## Practice: safe only

```bash
find . -name "*.tmp" -print
find . -name "*.tmp" -ls
find . -name "*.tmp" -ok ls -l {} \;
```

Do not delete yet.

## Required safety sentence

Before any deletion, say:

```text
I have previewed the exact files.
I am in the correct directory.
The search is not broader than intended.
I can recreate the practice files if needed.
```

---

# End-of-session written reflection

Answer in writing:

1. What does Effective Shell explain better than Author/Shotts for this topic?
2. What did Author/Shotts explain more completely?
3. What is the difference between `-name` and `-path`?
4. Why are quotes necessary around wildcard patterns?
5. Why is `find` better understood as a search expression language?
