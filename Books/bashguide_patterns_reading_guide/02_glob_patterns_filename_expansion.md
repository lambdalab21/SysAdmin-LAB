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

## Before touching the keyboard

Answer:

1. What does `*` match? Any string of characters 
2. What does `?` match? Any one character
3. What does `[abc]` match? Any one character from the set inside the brackets
4. What does “implicitly anchored” mean? The glob must match the entire filename. 
5. Why is `a*` different from regex `a.*`? glob a* must match the whole name. Regex a.* can match anywhere in the string. 
6. Does `*` normally match `/` inside pathname expansion? no. 

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

---

## Exercise 2: Implicit anchoring

Create a string test:

```bash
name='cat'
[[ $name = a* ]] && echo yes || echo no
[[ $name = ca* ]] && echo yes || echo no
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
Did the glob split this filename into three arguments? No. The filename stays as one argument. 
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

---

## Concept check after reading

Answer:

1. What does the shell do with `logs/*.log` before running the command? Expands the glob into a sorted list of matching filenames and passes them as separate arguments. 
2. Why is `printf '<%s> Safe way to see exactly what files a glob would match without running dangerous commands.
3. Why is globbing usually better than parsing `ls` output? Globs handle spaces, special characters, and hidden files correctly. 
4. Why does quoting a glob prevent filename expansion? Quotes turn the glob into a string. The shell does not expand it. 
