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
# Session 6: Final Lab and Self-Test

## Purpose

This file checks whether he can use Bash patterns effectively and safely.

No notes at first.

If he cannot explain a command, he should not use it with `rm`, `mv`, `cp`, or `chmod`.

---

# Part 1: Identify the pattern type

For each command, label the pattern type and who interprets it.

```bash
echo *.txt
echo "*.txt"
printf '<%s>
' docs/[Rr]*.md
find . -name "*.txt"
grep '[0-9]' logs/app-2026.log
[[ $file = *.txt ]]
[[ $line =~ ^ERROR ]]
echo file{1..3}.txt
printf '<%s>
' @(*.jpg|*.bmp)
```

Use this answer format:

```text
Command:
Pattern type:
Interpreter:
What expands or matches:
Risk:
```

---

# Part 2: Write commands from English

## 1. Preview all `.txt` files in the current directory.

Expected pattern:

```bash
printf '<%s>
' *.txt
```

## 2. Preview all `.log` files in the `logs` directory.

Expected pattern:

```bash
printf '<%s>
' logs/*.log
```

## 3. Test whether a variable contains a `.jpg` filename.

Expected pattern:

```bash
[[ $file = *.jpg ]]
```

## 4. Test whether a line begins with `ERROR` using regex.

Expected pattern:

```bash
[[ $line =~ ^ERROR ]]
```

## 5. Generate `chapter-01.md` through `chapter-05.md`.

Expected pattern:

```bash
echo chapter-{01..05}.md
```

## 6. With extglob enabled, preview files that are not `.jpg` or `.bmp`.

Expected pattern:

```bash
shopt -s extglob
printf '<%s>
' !(*.jpg|*.bmp)
```

---

# Part 3: Diagnose mistakes

## Mistake 1

```bash
for f in $(ls); do echo "$f"; done
```

Explain the problem:

```text
It parses ls output and can split filenames with spaces.
```

Better:

```bash
for f in *; do printf '<%s>
' "$f"; done
```

---

## Mistake 2

```bash
find . -name *.txt
```

Explain the problem:

```text
The shell may expand *.txt before find receives it.
```

Better:

```bash
find . -name "*.txt"
```

---

## Mistake 3

```bash
grep '*.txt' filelist.txt
```

Explain the problem:

```text
This is regex, not glob. In regex, * repeats the previous thing.
```

Better examples depending on intent:

```bash
grep '\.txt$' filelist.txt
```

or for shell filename selection:

```bash
printf '<%s>
' *.txt
```

---

## Mistake 4

```bash
rm !(*.jpg|*.bmp)
```

Explain the risk:

```text
Extended negative globs can match more than expected. Preview first.
```

Safer:

```bash
printf 'Would remove: <%s>
' !(*.jpg|*.bmp)
```

---

# Part 4: Feynman explain-back

Explain these to a younger student:

```bash
printf '<%s>
' tmp/*
find . -name "*.txt"
grep '[0-9]' file.txt
[[ $name = *.txt ]]
[[ $line =~ ^[0-9]+$ ]]
echo file{1..3}.txt
```

For each:

```text
Who reads the pattern?
Does the pattern expand to filenames?
Does it match text?
Does it generate words?
What would be dangerous if misunderstood?
```

---

# Part 5: Retention schedule

Repeat this after:

```text
1 day
1 week
1 month
```

Commands to explain from memory:

```bash
printf '<%s>
' *
printf '<%s>
' "*"
printf '<%s>
' logs/*.log
for f in tmp/*; do printf '<%s>
' "$f"; done
[[ $file = *.txt ]]
[[ $line =~ ^ERROR ]]
echo file{1..3}.txt
find . -name "*.txt"
grep '[0-9]' file.txt
```

---

# Final standard

He is effective enough with Bash patterns if he can say:

```text
I know when the shell expands a glob.
I know when [[ ]] uses a glob for string matching.
I know regex is different from glob.
I know brace expansion generates words and does not check files.
I preview globs before destructive commands.
I do not parse ls output to enumerate files.
I can explain who interprets every pattern I type.
```
