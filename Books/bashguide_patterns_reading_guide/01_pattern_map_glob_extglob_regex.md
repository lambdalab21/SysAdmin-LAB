# BashGuide Patterns Reading Guide

Source: Greg's Wiki, `BashGuide/Patterns`  
URL: https://mywiki.wooledge.org/BashGuide/Patterns

Purpose: build enough glob/pattern skill to be effective in Bash, and to prevent confusing shell globs with regex.

Core discipline:

```text
Do not type a pattern first.
First decide who interprets the pattern: the shell, [[ ... ]], case, grep, sed, or awk.
```

Before every exercise, answer:

```text
1. Am I matching filenames or text strings?
2. Who interprets the pattern?
3. Is this a glob, extended glob, regex, or brace expansion?
4. What should match?
5. What should not match?
6. Can I preview safely with printf before using cp, mv, or rm?
```

One-time lab setup:

```bash
mkdir -p ~/bashguide-patterns-lab/{docs,logs,images,archive,tmp}
cd ~/bashguide-patterns-lab

touch docs/report.txt docs/report-old.txt docs/README.md docs/README.TXT
touch logs/app.log logs/app-2026.log logs/error.log logs/debug.tmp
touch images/tokyo.jpg images/california.bmp images/names.txt
touch archive/old-report.txt archive/old.log
touch tmp/junk.tmp tmp/'file with spaces.txt' tmp/.hidden.tmp
```

Safe preview command for all glob practice:

```bash
printf '<%s>
' PATTERN
```

Replace `PATTERN` with the glob you are testing.

---
# Session 1: Pattern Map — Glob, Extended Glob, Regex, Brace Expansion

## Read before exercises

Read the opening of BashGuide/Patterns, from the beginning through the short definition of “Pattern.”

## What he should gain

He should gain a map of the territory:

```text
glob           = mostly filename matching / simple string matching
extended glob  = more powerful glob syntax, still glob-style
regex          = string matching, not filename expansion
brace expansion = word generation, not pattern matching
```

Do not let him treat all pattern symbols as “regex.” That is the mistake this guide is designed to prevent.

---

## Feynman analogy

Imagine three different people can read a pattern:

```text
The shell reads globs before the command starts.
[[ ... ]] can use globs to test a string.
grep/sed/awk read regex after they start.
```

Brace expansion is not really matching. It is like a word-generating machine.

```bash
echo file{1..3}.txt
```

means:

```text
Generate file1.txt file2.txt file3.txt.
```

It does not ask whether those files exist.

---

## Before touching the keyboard

Answer:

1. What is a pattern?
2. Which kind of pattern selects filenames on the command line?
3. Can regex select filenames in normal shell expansion?
4. What is the difference between matching and generating?
5. Which later tools use regex: `grep`, `sed`, `awk`, or `ls`?

---

## Exercise 1: Who interprets this pattern?

For each command, write who interprets the pattern.

```bash
ls *.txt
find . -name "*.txt"
grep '[0-9]' docs/report.txt
[[ $name = *.txt ]]
[[ $line =~ ^[0-9]+$ ]]
echo file{1..3}.txt
```

Expected thinking:

```text
ls *.txt                  shell expands glob before ls runs
find . -name "*.txt"      find receives a name pattern
 grep '[0-9]' file         grep receives a regex
[[ $name = *.txt ]]       Bash [[ ]] uses glob matching
[[ $line =~ ... ]]        Bash [[ ]] uses regex matching
echo file{1..3}.txt       Bash performs brace expansion
```

---

## Exercise 2: Explain it to a younger student

Explain this difference:

```bash
echo *.txt
echo "*.txt"
```

Use:

```text
In the first command, the shell...
In the second command, quotes...
The echo command receives...
```

---

## Concept check after reading

Answer without notes:

1. Why is “glob versus regex” not just vocabulary?
2. Why can `ls *.txt` behave differently from `grep '*.txt' file`?
3. Why is `echo` useful for learning expansion?
4. What habit prevents dangerous glob mistakes?

Required habit:

```text
Preview expansions before using rm, mv, cp, or chmod.
```
