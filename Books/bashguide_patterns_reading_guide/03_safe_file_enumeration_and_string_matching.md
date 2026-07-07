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
# Session 3: Safe File Enumeration and Glob Matching in `[[ ... ]]`

## Read before exercises

Reread the part of “Glob Patterns” that shows globs expanding to filenames and the part that shows using a glob inside `[[ ... ]]` to check a string.

## What he should gain

He should learn two effective Bash habits:

```text
Use globs, not ls, to enumerate files.
Use [[ string = glob ]] for simple string classification.
```

He should also learn the discipline of separating:

```text
filename expansion outside [[ ... ]]
string matching inside [[ ... ]]
```

---

## Feynman analogy

Outside `[[ ... ]]`, the shell uses a glob to look for filenames.

Inside:

```bash
[[ $filename = *.jpg ]]
```

Bash uses the glob as a rule to test the string stored in the variable.

It does not expand to filenames there.

---

## Before touching the keyboard

Answer:

1. What is the difference between `echo *.jpg` and `[[ $file = *.jpg ]]`?
2. Why is `for file in *` better than `for file in $(ls)`?
3. Why must `"$file"` be quoted inside the loop body?
4. What does `[[ $name = *.txt ]]` return: text output or exit status?

---

## Exercise 1: Safe enumeration

```bash
cd ~/bashguide-patterns-lab

for file in tmp/*; do
    printf 'FILE: <%s>
' "$file"
done
```

Explain every part:

```text
tmp/* expands to...
file variable stores...
"$file" is quoted because...
printf shows angle brackets because...
```

---

## Exercise 2: Classify filenames

```bash
for file in images/*; do
    if [[ $file = *.jpg ]]; then
        printf 'JPEG: %s
' "$file"
    elif [[ $file = *.bmp ]]; then
        printf 'BMP: %s
' "$file"
    elif [[ $file = *.txt ]]; then
        printf 'TEXT: %s
' "$file"
    else
        printf 'OTHER: %s
' "$file"
    fi
done
```

Before running, predict:

```text
Which file will be JPEG?
Which file will be BMP?
Which file will be TEXT?
```

After running, answer:

```text
Did [[ ]] expand *.jpg into filenames?
Or did it use *.jpg as a string-matching pattern?
```

---

## Exercise 3: Quote discipline

Test:

```bash
file='tmp/file with spaces.txt'
[[ $file = *.txt ]] && echo txt
[[ "$file" = *.txt ]] && echo txt-quoted
```

Both should work here.

Question:

```text
Why is quoting variables still a good general habit, even if [[ ]] is safer than old [ ] tests?
```

Expected idea:

```text
It makes intent clear and avoids carrying bad habits into other contexts.
```

---

## Exercise 4: Man/help habit

Use built-in help:

```bash
help [[ 2>/dev/null || true
help for
help if
```

Use Bash manual search if available:

```bash
man bash
```

Inside `man bash`, search for:

```text
Pattern Matching
Compound Commands
```

Answer:

```text
Where did you find information about [[ ]]?
Where did you find information about pattern matching?
What was hard to read in the man page?
```

The goal is not to understand the whole manual. The goal is to build the habit of checking it.

---

## Concept check after reading

Answer:

1. Why is `for file in *` safer than `for file in $(ls)`?
2. What does `[[ $file = *.txt ]]` test?
3. Does the pattern in `[[ $file = *.txt ]]` select filenames from the filesystem?
4. Why should variables usually be quoted when passed to commands?
5. What documentation command can help you inspect Bash builtins?
