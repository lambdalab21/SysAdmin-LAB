# Effective Shell Ch. 9 Review Quiz and Retention Drills

Use this after Day 1 and Day 2.

The goal is retrieval, not rereading.

---

# Part 1: Concept quiz

Answer without notes.

## Script basics

1. What is a shell script?
2. Why is a script better than repeatedly typing the same pipeline?
3. What does a comment do?
4. What is the difference between a useful comment and a noisy comment?
5. Why should comments often explain “why” rather than “what”?

## Pipeline review

Explain each stage:

```bash
tail ~/.bash_history -n 1000 \
    | sort \
    | uniq -c \
    | sed 's/^ *//' \
    | sort -n -r \
    | head -n 10
```

Questions:

1. Why use `tail`?
2. Why must `sort` come before `uniq -c`?
3. What does `uniq -c` add?
4. What does `sed 's/^ *//'` remove?
5. Why use `sort -n -r`?
6. Why use `head -n 10`?

## Running scripts

1. What is the difference between these?

```bash
bash script.sh
./script.sh
source script.sh
```

2. What does `chmod +x script.sh` do?
3. What does the shebang do?
4. Why might `#!/usr/bin/env bash` be useful?
5. What is the risk of sourcing a script?

## PATH and installation

1. What is `$PATH`?
2. What does `type command-name` show?
3. Why might `~/.local/bin` be appropriate for personal scripts?
4. What is a symlink?
5. What does this do?

```bash
ln -sf ~/scripts/common.v1.sh ~/.local/bin/common
```

---

# Part 2: Debugging drill

## Drill 1: Find the bug

This script does not work reliably:

```bash
#!/usr/bin/env bash

echo "common commands:"
tail ~/.bash_history -n 1000 \ # get recent history
    | sort \
    | uniq -c \
    | sed 's/^ *//' \
    | sort -n -r \
    | head -n 10
```

Questions:

1. What is wrong?
2. Why does the backslash fail here?
3. How should the comment be moved?

## Drill 2: Explain the execution method

For each command, write whether it uses a new shell or the current shell:

```bash
bash ~/scripts/common.v1.sh
~/scripts/common.v1.sh
source ~/scripts/set-lab-editor.sh
. ~/scripts/set-lab-editor.sh
```

## Drill 3: Safer installation check

Before running `common`, check:

```bash
type common
ls -l ~/.local/bin/common
readlink ~/.local/bin/common
```

Explain:

1. Which file is the command name pointing to?
2. Is it a symlink?
3. What script will actually run?

---

# Part 3: Small independent task

Create a new personal command named:

```text
recent-md
```

Goal:

```text
Show the ten most recently modified Markdown files under the current directory.
```

Rules:

1. Do not type the final script first.
2. Build the pipeline in the shell first.
3. Explain each stage.
4. Save it as a script.
5. Add a shebang.
6. Make it executable.
7. Link it into `~/.local/bin`.
8. Verify with `type recent-md`.

Possible starting pipeline:

```bash
find . -type f -name '*.md' -printf '%T@ %p\n' | sort -n -r | head -n 10
```

If `find -printf` does not work on the system, write down why and look up the system's `find` manual.

## Before typing

Fill this out:

```text
Command name:
Problem it solves:
Input:
Output:
Pipeline stages:
Possible false assumptions:
Test directory:
```

## Explain-back

After finishing, explain:

```text
I created a script because this is a reusable command.
I tested the pipeline first.
The shebang selects Bash.
The executable bit lets the script run by path.
The symlink in ~/.local/bin lets PATH find it as recent-md.
```

---

# Final mastery standard

He is ready to move on if he can answer:

```text
When should I run a script?
When should I source a script?
When should I add a shebang?
When should I install a personal command?
How do I inspect what command will run?
How do I explain every stage of a pipeline inside a script?
```

If he cannot explain one of those, reread the relevant Day 1 or Day 2 section and repeat the smallest drill.
