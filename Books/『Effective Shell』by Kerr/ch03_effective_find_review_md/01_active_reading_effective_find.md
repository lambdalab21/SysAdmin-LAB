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
# Session 1: Active Reading — Effective Use of `find`

## Reading target

Read Effective Shell Chapter 3, “Finding Files.” Focus on the chapter’s structure:

```text
introducing find
searching for files or folders only
searching by name
searching by path
combining tests with AND / OR
case-insensitive searches
NOT and grouping
actions such as print, exec, ok, delete
```

Do not read passively. Stop after each idea and test whether you can explain it without looking.

---

# Feynman analogy before reading

Imagine you are asking someone to search a school building.

A vague instruction is bad:

```text
Find the thing.
```

An effective instruction is precise:

```text
Start in the science hallway.
Look only in classrooms.
Find rooms whose names include “lab.”
Do not enter locked rooms.
Write down the room numbers.
```

That is `find`.

```bash
find science-hallway -type d -name '*lab*' -print
```

The effective user does not ask “What command do I type?” first.

The effective user asks:

```text
What exactly am I trying to find?
Where should I search?
How can I make the search smaller and safer?
```

---

# Reading checkpoint 1: Plain `find`

After reading the introduction, answer before running commands:

```text
What will plain find show?
Will it include directories?
Will it include children of directories?
Why might find . be clearer than find?
```

Practice:

```bash
cd ~/es-ch03-find-review
find .
find . | head
find . | wc -l
```

Explain-back:

```text
`find .` starts at the current directory and walks the tree.
Without tests, it prints everything it finds.
```

Effective habit:

```text
If output is huge, first add a limit or pipe to head.
Do not stare at uncontrolled output and pretend that is understanding.
```

---

# Reading checkpoint 2: `-type`

Before running:

```text
Which results should be files?
Which results should be directories?
Why is -type f often a good habit?
```

Practice:

```bash
find . -type f
find . -type d
find . -type f | wc -l
find . -type d | wc -l
```

Explain-back:

```text
`-type f` means ordinary files.
`-type d` means directories.
Using `-type` makes the search more precise.
```

Effective question:

```text
Am I searching for files, folders, or either?
```

If he cannot answer, he is not ready to write the command.

---

# Reading checkpoint 3: `-name`

Before running:

```text
What is the difference between a file name and a path?
What should match *log*?
Why must the pattern be quoted?
```

Practice:

```bash
find . -name '*log*'
find . -type f -name '*log*'
find . -type d -name '*log*'
find . -type f -name '*.logs'
```

Bad habit demonstration:

```bash
echo *log*
```

Explain-back:

```text
The shell can expand wildcards before `find` sees them.
Quote wildcard patterns intended for `find`.
```

Effective habit:

```text
Use `-type f` with `-name` when you really mean files.
Use `-type d` when you really mean directories.
```

---

# Reading checkpoint 4: `-path`

Feynman explanation:

```text
-name checks the label on the object.
-path checks the full address to the object.
```

Before running:

```text
Why will -name '*apm-logs*' not find every file inside apm-logs?
Why will -path '*apm-logs*' find them?
```

Practice:

```bash
find . -name '*apm-logs*'
find . -path '*apm-logs*'
find . -type f -path '*apm-logs*'
```

Explain-back:

```text
`-name` tests the final filename only.
`-path` tests the whole path.
```

Effective question:

```text
Am I searching by object name or by location inside the tree?
```

---

# Reading checkpoint 5: logic — AND, OR, grouping, NOT

Before running:

```text
What does implicit AND mean?
When do I need OR?
Why do OR expressions need grouping?
What does NOT exclude?
```

Practice one stage at a time:

```bash
find . -name '*.js'
find . -name '*.html'
find . \( -name '*.js' -or -name '*.html' \)
find . \( -name '*.js' -or -name '*.html' \) -type f
find . \( -name '*.js' -or -name '*.html' \) -type f -not -path '*programs*'
```

Explain-back:

```text
The parentheses group the OR expression.
The `-not -path '*programs*'` part excludes paths containing programs.
```

Effective habit:

```text
Build logical expressions in stages.
Do not type a complex `find` expression in one shot.
```

---

# Reading checkpoint 6: case-insensitive search

Before running:

```text
Which files will -name '*.html' miss?
Which files will -iname '*.html' include?
When is case-insensitive search useful?
When could it hide carelessness?
```

Practice:

```bash
find . -type f -name '*.html'
find . -type f -iname '*.html'
```

Explain-back:

```text
`-name` is case-sensitive.
`-iname` is case-insensitive.
```

---

# Reading checkpoint 7: actions and risk

Before reading/using actions, write:

```text
A search command only observes.
An action command changes something or runs another program.
```

Practice safe actions:

```bash
find . -type f -name '*.tmp' -print
find . -type f -name '*.tmp' -ls
find . -type f -name '*.tmp' -ok ls -l {} \;
```

Do not delete in this session.

Explain-back:

```text
`-print` shows paths.
`-ls` shows details.
`-ok` asks before running an action.
`{}` becomes the current match.
```

---

# End-of-reading reflection

Answer in writing:

1. What did Effective Shell add beyond Shotts Chapter 17?
2. What is the difference between `-name` and `-path`?
3. Why should OR expressions be grouped?
4. Why should wildcard patterns be quoted?
5. What makes a `find` command “effective” rather than just correct?
