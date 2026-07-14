#Book/Effective-Shell #Author/Kerr
#quiz 
# Review Quiz and Retention Drills

Use this after finishing Effective Shell Chapter 5.

This file refreshes:

```text
TLCL Ch. 19 — regex thinking
TLCL Ch. 20 — text-processing pipelines
Effective Shell Ch. 5 — effective grep usage
```

## Retrieval quiz: answer without notes

1. What does `grep` normally print?
2. What does `grep -i` do?
3. What does `grep -E` do?
4. What is the difference between `grep pattern file` and `command | grep pattern`?
5. What do `^` and `$` mean in regex?
6. What does `[[:space:]]*` mean?
7. What does `grep -v` do?
8. What does `grep -A 3` do?
9. What does `grep -B 3` do?
10. What does `grep -C 3` do?
11. Why are `-H` and `-n` useful together?
12. What does `grep -R` do?
13. Why can `grep -i err` produce false positives?
14. Why is `grep '[installed]'` usually wrong if you mean literal `[installed]`?
15. When is `awk` better than `grep`?
16. When is `sed` better than `grep`?
17. Why inspect before counting?
18. Why should command output often be piped to `less`?
19. Why master core `grep` before `ripgrep`?
20. What is the disciplined pattern-design process?

## Drill 1: explain every command

For each command, write what it does and what could go wrong.

```bash
grep INFO app.log

grep -i error app.log

grep -E '^(ERROR|WARN)' app.log

grep -E '^[[:space:]]*PasswordAuthentication' sshd_config.sample

grep -Ev '^[[:space:]]*(#|$)' sshd_config.sample

grep -R -Hn -C 2 -i error logs

grep '\[installed\]' packages.txt
```

For each command, answer:

```text
Tool:
Input source:
Pattern:
Regex flavor:
Expected matches:
Possible false positives:
Possible false negatives:
```

## Drill 2: build the command from the question

Write the command before checking the answer.

### Question 1

Show lines in `app.log` containing `WARN`.

Answer:

```bash
grep WARN app.log
```

### Question 2

Show lines containing `ERROR` or `WARN`.

Answer:

```bash
grep -E ' ERROR | WARN ' app.log
```

### Question 3

Show active, non-comment `PasswordAuthentication` lines.

Answer:

```bash
grep -E '^[[:space:]]*PasswordAuthentication' sshd_config.sample
```

### Question 4

Show all non-comment, non-blank config lines.

Answer:

```bash
grep -Ev '^[[:space:]]*(#|$)' sshd_config.sample
```

### Question 5

Search recursively for error-like lines with filenames and line numbers.

Answer:

```bash
grep -R -Hn -i error logs
```

### Question 6

Search recursively for error-like lines with two lines of context.

Answer:

```bash
grep -R -Hn -C 2 -i error logs
```

### Question 7

Find literal `[installed]` package lines.

Answer:

```bash
grep '\[installed\]' packages.txt
```

## Drill 3: false-positive discipline

For each pattern, write one false positive it might match.

```text
err
ERROR
[installed]
[0-9]
PasswordAuthentication
.*
```

Then improve at least three of them.

## Drill 4: pipeline discipline

Build each pipeline one stage at a time.

### Task A

Count warning/error lines.

```bash
grep -E ' WARN | ERROR ' app.log

grep -E ' WARN | ERROR ' app.log | wc -l
```

Explain why the first line should be run before the second.

### Task B

Summarize log levels.

```bash
awk '{ print $2 }' app.log

awk '{ print $2 }' app.log | sort

awk '{ print $2 }' app.log | sort | uniq -c
```

Explain what each stage adds.

### Task C

Show active config lines and page them.

```bash
grep -Ev '^[[:space:]]*(#|$)' sshd_config.sample | less
```

Explain why this is useful with larger files.

## Final explain-back

Explain Chapter 5 to a younger student:

```text
grep is a tool for searching or filtering text.
It usually prints matching lines.
Regex makes the match rule more precise.
The context flags show nearby lines.
-Hn tells me where the evidence came from.
-v removes matching lines, which is useful but can hide important information.
grep becomes much more powerful when combined with pipelines.
```

Then answer:

```text
What did Effective Shell Ch. 5 add beyond TLCL Ch. 19 and 20?
```

Expected answer:

```text
TLCL taught the regex and text-processing foundations.
Kerr Ch. 5 shows how to use grep as a practical investigation tool:
search history, find problems, use context, search multiple files, invert matches, and combine grep with pipelines.
```
