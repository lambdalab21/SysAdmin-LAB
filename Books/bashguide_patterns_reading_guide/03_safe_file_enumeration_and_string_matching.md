# BashGuide Patterns Reading Guide

Source: Greg's Wiki, `BashGuide/Patterns`  
URL: https://mywiki.wooledge.org/BashGuide/Patterns

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

---
# Session 3: Safe File Enumeration and Glob Matching in `[[ ... ]]`

## Read before exercises

Reread the part of “Glob Patterns” that shows globs expanding to filenames and the part that shows using a glob inside `[[ ... ]]` to check a string.

---

## Before touching the keyboard

Answer:

1. What is the difference between `echo *.jpg` and `[[ $file = *.jpg ]]`? echo *.jpg expands  the glob to matching filenames. [[ $file = *.jpg]] uses the glob as a pattern for string matching against the variable.
2. Why is `for file in *` better than `for file in $(ls)`? globs preserve filenames with spaces/special characters as single items. $(ls) splits on whitespace and breaks on spaces. 
3. Why must `"$file"` be quoted inside the loop body? To prevent word-splitting and glob expansion on the variable's value.
4. What does `[[ $name = *.txt ]]` return: text output or exit status? Exit status(0 for true/match, non-zero or false)

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
tmp/* expands to all non-hidden files in the tmp/ directory. 
file variable stores one complete filenames per iteration. 
"$file" is quoted because it prevents word-splitting and accidental globbing.
printf shows angle brackets because they visually mark the exact stringcontent. 
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
Which file will be JPEG? images/tokyo.jpg
Which file will be BMP? images/california.bmp
Which file will be TEXT? images/names.txt
```

After running, answer:

```text
Did [[ ]] expand *.jpg into filenames? No. 
Or did it use *.jpg as a string-matching pattern? Yes. 
```

---

## Exercise 3: Man/help habit

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
Where did you find information about [[ ]]? Under "compound command" in man bash or help [[
Where did you find information about pattern matching? In the "pattern making" match of man bash. 
What was hard to read in the man page? Dense technical language and many cross-references. 
```
