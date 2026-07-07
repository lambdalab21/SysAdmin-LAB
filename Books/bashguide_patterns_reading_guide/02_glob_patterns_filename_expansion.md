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
# Session 2: Glob Patterns and Filename Expansion

## Read before exercises

Read the “Glob Patterns” section through the good-practice note about using globs instead of `ls` to enumerate files.

## What he should gain

He should gain practical glob fluency:

```text
*        any string, including empty
?        exactly one character
[...]    one character from the set
```

He should also understand:

```text
Globs are implicitly anchored.
The shell expands filename globs into matching filenames before the command runs.
The results are sorted.
Filenames containing spaces remain single arguments after glob expansion.
```

---

## Feynman analogy

A glob is a filename filter used by the shell.

The shell looks in the current directory and asks:

```text
Which filenames match this pattern as a whole filename?
```

Then it replaces the pattern with the matching filenames.

```bash
echo a*
```

is not really what `echo` sees. The shell may turn it into:

```bash
echo app.log archive
```

first.

---

## Before touching the keyboard

Answer:

1. What does `*` match?
2. What does `?` match?
3. What does `[abc]` match?
4. What does “implicitly anchored” mean?
5. Why is `a*` different from regex `a.*`?
6. Does `*` normally match `/` inside pathname expansion?

---

## Exercise 1: Preview ordinary globs

```bash
cd ~/bashguide-patterns-lab

printf '<%s>
' *
printf '<%s>
' docs/*
printf '<%s>
' logs/*.log
printf '<%s>
' logs/app?.log
printf '<%s>
' docs/README.*
printf '<%s>
' images/*.[jb][mp][gp]
```

For each command, answer:

```text
What pattern did the shell see?
What filenames matched?
What arguments did printf receive?
```

---

## Exercise 2: Implicit anchoring

Create a string test:

```bash
name='cat'
[[ $name = a* ]] && echo yes || echo no
[[ $name = ca* ]] && echo yes || echo no
```

Explain:

```text
Why does a* not match cat?
Why does ca* match cat?
```

Important idea:

```text
A glob must match the whole string in [[ string = glob ]].
```

---

## Exercise 3: Spaces in filenames

Preview:

```bash
printf '<%s>
' tmp/*
```

Observe `file with spaces.txt`.

Question:

```text
Did the glob split this filename into three arguments?
```

Expected answer:

```text
No. Filename expansion happens after word splitting, so the matched filename remains one argument.
```

---

## Exercise 4: Do not parse `ls`

Bad pattern to understand, not copy as a habit:

```bash
for file in $(ls tmp); do printf '<%s>
' "$file"; done
```

Better:

```bash
for file in tmp/*; do printf '<%s>
' "$file"; done
```

Explain:

```text
Why does the ls version break with spaces?
Why does the glob loop handle the filename correctly?
```

---

## Concept check after reading

Answer:

1. What does the shell do with `logs/*.log` before running the command?
2. Why is `printf '<%s>
' *.txt` a good preview habit?
3. Why is globbing usually better than parsing `ls` output?
4. Why does quoting a glob prevent filename expansion?
5. What is the danger of using `rm *` without preview?
