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
# Session 5: Regex and Brace Expansion Contrast

## Read before exercises

Read the “Regular Expressions” section and the “Brace Expansion” section.

## What he should gain

He should learn three contrasts:

```text
Glob expands filenames or checks simple string patterns.
Regex checks whether a string matches a more complex rule.
Brace expansion generates words; it does not match existing filenames.
```

He should also understand that Bash regex is mainly used in:

```bash
[[ $var =~ regex ]]
```

not in ordinary filename selection.

---

## Feynman analogy

Glob is like asking:

```text
Which files in this directory match this filename pattern?
```

Regex is like asking:

```text
Does this string fit this rule?
```

Brace expansion is like asking:

```text
Please generate this list of words.
```

---

## Before touching the keyboard

Answer:

1. Can regex select filenames in normal shell expansion?
2. What Bash operator uses regex inside `[[ ... ]]`?
3. What variable stores captured regex groups?
4. Is brace expansion pattern matching?
5. Which happens first: brace expansion or filename expansion?

---

## Exercise 1: Regex in Bash

```bash
line='ERROR 2026 app01 failed-login alice'

[[ $line =~ ^ERROR ]] && echo 'starts with ERROR'
[[ $line =~ [0-9]{4} ]] && echo 'contains four digits'
```

Explain:

```text
Who interpreted ^ERROR?
Was this glob or regex?
Did this select filenames?
```

---

## Exercise 2: Regex variable habit

```bash
re='^ERROR [0-9]{4}'
line='ERROR 2026 app01 failed-login alice'

if [[ $line =~ $re ]]; then
    echo 'matched'
else
    echo 'not matched'
fi
```

Answer:

```text
Why might storing regex in a variable be clearer than putting a complex regex directly inside [[ ]]?
```

---

## Exercise 3: Brace expansion generates words

```bash
echo file{1..3}.txt
echo {a,b,c}.log
echo chapter-{01..05}.md
```

Now check whether files exist:

```bash
ls file{1..3}.txt 2>/dev/null || echo 'these generated names may not exist'
```

Explain:

```text
Did brace expansion care whether the files existed?
```

Expected answer:

```text
No. Brace expansion generates words. It does not match existing filenames.
```

---

## Exercise 4: Brace expansion before glob expansion

```bash
cd ~/bashguide-patterns-lab
printf '<%s>
' {docs,logs}/*
```

Explain the two stages:

```text
First, brace expansion produced...
Then, filename expansion matched...
```

---

## Exercise 5: Compare four pattern-like commands

```bash
cd ~/bashguide-patterns-lab

printf '<%s>
' logs/*.log
find . -name "*.log"
grep '[0-9]' logs/app-2026.log
printf '<%s>
' logs/app-{2025,2026}.log
```

For each one:

```text
Is it glob, find name pattern, regex, or brace expansion?
Who interprets it?
Does it require existing files to match?
```

---

## Concept check after reading

Answer:

1. Why can regex not be used to select filenames by normal shell expansion?
2. What is `=~` used for?
3. What is `BASH_REMATCH` used for?
4. Why is brace expansion not globbing?
5. Why does `echo {1..3}` work even if no files exist?
