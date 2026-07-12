#Book/Effective-Shell #Author/Kerr 
#drill #find #search 
# Effective Shell Chapter 3: Finding Files — Review After Author/Shotts Chapter 17

Purpose:

```text
Author/Shotts Chapter 17 taught the tools.
Effective Shell Chapter 3 should teach better judgment.
```

Use the Author/Shotts pattern:

```text
find WHERE TESTS ACTION
```

Use the Effective Shell mental model:

```text
find [starting point...] [expression]
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
# Reading checkpoint 1: Plain `find`

After reading the introduction, answer before running commands:

```text
What will plain find show? Plain find. 
Will it include directories? Everything under the starting point. 
Will it include children of directories? Yes. 
Why might find . be clearer than find? It shows the search stats in the current directory. 
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
Which results should be files? -type f
Which results should be directories? -type d
Why is -type f often a good habit? It makes searches more precise and avoids acting on directories accidentally. 
```

Practice:

```bash
find . -type f
find . -type d
find . -type f | wc -l
find . -type d | wc -l
```

---

# Reading checkpoint 3: `-name`

Before running:

```text
What is the difference between a file name and a path? Name is just the last thing. Path is the full location.   
What should match *log*? Any file or directory containing "log" in it's name. 
Why must the pattern be quoted? To prevent the shell from expanding the wildcards before find runs. 
```

Practice:

```bash
find . -name '*log*'
find . -type f -name '*log*'
find . -type d -name '*log*'
find . -type f -name '*.logs'
```
---

# Reading checkpoint 4: `-path`

Before running:

```text
Why will -name '*apm-logs*' not find every file inside apm-logs? It only matches with the name of the item itself. 
Why will -path '*apm-logs*' find them? It matches anywhere in the full path. 
```

Practice:

```bash
find . -name '*apm-logs*'
find . -path '*apm-logs*'
find . -type f -path '*apm-logs*'
```

---

# Reading checkpoint 5: logic — AND, OR, grouping, NOT

Before running:

```text
What does implicit AND mean? Multiple tests are combined with AND by default
When do I need OR? When you want any of several conditions to match. 
Why do OR expressions need grouping? To control which tests are part of the OR. 
What does NOT exclude? Items that match the negated test. 
```

Practice one stage at a time:

```bash
find . -name '*.js'
find . -name '*.html'
find . \( -name '*.js' -or -name '*.html' \)
find . \( -name '*.js' -or -name '*.html' \) -type f
find . \( -name '*.js' -or -name '*.html' \) -type f -not -path '*programs*'
```

---

# Reading checkpoint 6: case-insensitive search

Before running:

```text
Which files will -name '*.html' miss?` README.html
Which files will -iname '*.html' include? Both .html and .HTML
When is case-insensitive search useful? When filenames have inconsistent cases. 
When could it hide carelessness? It can match unintended files if you're not careful. 
```

Practice:

```bash
find . -type f -name '*.html'
find . -type f -iname '*.html'
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


---

# End-of-reading reflection

Answer in writing:

1. What is the difference between `-name` and `-path`? `-name` matches only the filename; -path matches the full path. 
2. Why should OR expressions be grouped? Parentheses control evaluation order and group the OR logic correctly. 
3. Why should wildcard patterns be quoted? To stop the shell from expanding them before find receives the pattern. 
4. What makes a `find` command “effective” rather than just correct? It has a clear question, limited scope, and provides verification. 
