# BashGuide Patterns Reading Guide

Source: Greg's Wiki, `BashGuide/Patterns`  
URL: https://mywiki.wooledge.org/BashGuide/Patterns

Purpose: build enough glob/pattern skill to be effective in Bash, and to prevent confusing shell globs with regex.

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

---
## Before touching the keyboard

Answer:

1. What is a pattern? A string with special syntax designed to match filenames and check/validate data strings. 
2. Which kind of pattern selects filenames on the command line? Globs. 
3. Can regex select filenames in normal shell expansion? No. 
4. What is the difference between matching and generating? Matching tests if a string fits a pattern, generating shell expands the pattern into actual matching filenames. 
5. Which later tools use regex: `grep`, `sed`, `awk`, or `ls`?

---

## Exercise 1: Who interprets this pattern?

```bash
ls *.txt
find . -name "*.txt"
grep '[0-9]' docs/report.txt
[[ $name = *.txt ]]
[[ $line =~ ^[0-9]+$ ]]
echo file{1..3}.txt
```

---
## Concept check after reading

1. Why is “glob versus regex” not just vocabulary? They have different syntaxes, capabilities, and are interpreted by different parts of the system. 
2. Why can `ls *.txt` behave differently from `grep '*.txt' file`? ls *.txt makes shell expand the glob to filenames first. 
3. Why is `echo` useful for learning expansion? It safely shows what the shell expands the pattern into without side effects. 
4. What habit prevents dangerous glob mistakes? Always preview with printf '<%s>\n' PATTERN before using cp, mv, or rm. 
