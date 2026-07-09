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
# Session 4: Extended Globs

## Read before exercises

Read the “Extended Globs” section.

## What he should gain

He should learn that Bash has a more powerful glob mode, but it is not the first thing to reach for.

Extended globs are useful, but they add mental load.

Read for recognition and controlled practice:

```text
?(pattern-list)   zero or one
*(pattern-list)   zero or more
+(pattern-list)   one or more
@(pattern-list)   one of
!(pattern-list)   anything except
```

They require:

```bash
shopt -s extglob
```

---

## Feynman analogy

Ordinary glob:

```text
Match filenames ending in .jpg.
```

Extended glob:

```text
Match anything except files ending in .jpg or .bmp.
```

It is more expressive, but easier to misuse.

---

## Before touching the keyboard

Answer:

1. Are extended globs on by default?
2. What does `shopt` do?
3. Which operator means “one of these patterns”?
4. Which operator means “not these patterns”?
5. Why should he preview extended globs before using them with `rm`?

---

## Exercise 1: Enable and inspect

```bash
cd ~/bashguide-patterns-lab/images

shopt extglob
shopt -s extglob
shopt extglob
```

Answer:

```text
What changed after shopt -s extglob?
```

---

## Exercise 2: One-of pattern

```bash
printf '<%s>
' @(*.jpg|*.bmp)
```

Explain:

```text
@(...) means...
The | separates...
The matching files are...
```

---

## Exercise 3: Not-these pattern

```bash
printf '<%s>
' !(*.jpg|*.bmp)
```

Before running, predict:

```text
Which files should be excluded?
Which files should remain?
```

Important safety rule:

```text
Never use rm with !(...) until you have previewed the expansion.
```

---

## Exercise 4: Compare ordinary glob and extended glob

```bash
printf 'ordinary jpg:
'
printf '<%s>
' *.jpg

printf 'extended jpg or bmp:
'
printf '<%s>
' @(*.jpg|*.bmp)

printf 'extended not jpg or bmp:
'
printf '<%s>
' !(*.jpg|*.bmp)
```

Answer:

```text
Which command was easiest to understand?
Which command was most powerful?
Which command is riskiest if used with rm?
```

---

## Exercise 5: Turn it off

```bash
shopt -u extglob
shopt extglob
```

Answer:

```text
Why might a script explicitly enable extglob if it relies on it?
```

---

## Concept check after reading

Answer:

1. What does `@(a|b)` mean in extended glob syntax?
2. What does `!(a|b)` mean?
3. Why is `!(...)` potentially dangerous?
4. How do you enable extended globs?
5. Why should extended globs be delayed until ordinary globs are solid?
