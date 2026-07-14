#Book/Effective-Shell #Author/Kerr
#bash/command/grep 
# Day 1: `grep` as a Line Filter and Regex Refresher

## Read before exercises

Read Effective Shell Chapter 5 sections:

```text
What is Grep
Why Grep?
Searching Through Text
```

Also briefly review from TLCL:

```text
Chapter 19: literal matches, anchors, bracket expressions
Chapter 20: grep as one stage in a text-processing pipeline
```

## What he should gain

He should gain this idea:

```text
grep selects lines.
It can search files.
It can also filter stdin in a pipeline.
```

Do not let him think:

```text
grep finds words.
```

Better:

```text
grep prints lines that match a pattern.
```

## Before reading: Feynman preview

Explain this before reading:

```text
If a log file is a stack of paper, grep is a rule for choosing which sheets to keep.
The output is not “the match.”
The output is usually the whole matching line.
```

## After reading: concept questions

Answer without looking back:

1. What does `grep` do?
2. Why is `grep` useful even when not searching a file directly?
3. What happens when `grep` receives input from a pipe?
4. Why does searching for `err` find `error`, `stderr`, and other words containing those letters?
5. What does it mean that `grep` is a filter?
6. In what way does this connect to TLCL Chapter 20?

Do not do the exercises until these are answered.

---

# Exercise 1: Literal search

## Skill being gained

Use `grep` to answer a simple question from a text file.

## Do not type yet

Predict:

```text
Command idea:
grep INFO app.log

Expected output:
All lines containing the literal text INFO.

Possible false matches:
Any line containing INFO in another context.
```

## Run

```bash
cd ~/effective-shell-ch05-grep-lab
grep INFO app.log
```

## Inspect

Answer:

1. Did `grep` print only `INFO`, or whole lines?
2. How many lines matched?
3. Did this answer a useful question?
4. What question did it answer exactly?

---

# Exercise 2: grep from stdin

## Read before exercise

Reread the part where Kerr shows `grep` receiving piped input.

## Skill being gained

Understand that `grep` does not require a filename.

## Predict before typing

```text
What produces text?
What receives text?
What lines should survive the filter?
```

## Run

```bash
cat app.log | grep WARN
```

Then run the better direct form:

```bash
grep WARN app.log
```

## Explain-back

Answer:

1. Why do both commands produce the same matching lines?
2. Which command is simpler?
3. When is a pipe useful with `grep`?
4. Why is `cat file | grep pattern` often unnecessary?

---

# Exercise 3: Case sensitivity

## Read before exercise

Read the Chapter 5 part where Kerr searches for problems with `grep -i`.

## Skill being gained

Use case-insensitive search when logs are inconsistent.

## Predict before typing

```text
What will grep ERROR find?
What will grep -i error find that grep ERROR misses?
```

## Run

```bash
grep ERROR app.log

grep -i error app.log
```

## Explain-back

Answer:

1. Which command found more lines?
2. Why?
3. Did `grep -i error` also match anything that was not actually an error event?
4. Why can case-insensitive search be useful but slightly sloppy?

---

# Exercise 4: Search shell history safely

## Skill being gained

Use grep as a filter for command output.

## Predict before typing

Answer:

```text
What command produces my history?
What pattern am I looking for?
Would this search be private or sensitive on my real machine?
```

## Run

```bash
history | grep grep | tail -n 10
```

If the shell history is not available in this environment, explain why and skip the command.

## Explain-back

Answer:

1. What did `history` output?
2. What did `grep grep` keep?
3. What did `tail -n 10` do?
4. Which part comes from TLCL Chapter 20 pipeline thinking?

---

# Day 1 finish standard

He is done only if he can say:

```text
grep prints matching lines.
grep can read files or stdin.
-i is useful when case varies.
grep is often the first filter in a pipeline.
I should always know what question my grep command answers.
```
