#Book/Effective-Shell #Author/Kerr
# Day 4: Pipelines, Alternatives, and Final Lab

## Read before exercises

Read Effective Shell Chapter 5 sections:

```text
Don't Forget Your Pipelines!
Alternatives to Grep
Summary
```

Review TLCL Chapter 20:

```text
head
tail
wc
sort
uniq
cut
less
pipelines
```

## What he should gain

He should gain this idea:

```text
grep often narrows the data.
Other tools then count, sort, slice, page, or summarize it.
```

Kerr’s main practical message here is not “learn every grep option.”
It is:

```text
Use grep effectively with other tools.
Master core grep before reaching for alternatives.
```

## Before reading: Feynman preview

Explain this before reading:

```text
A pipeline is an assembly line.
grep usually removes irrelevant rows.
Then wc counts, sort orders, uniq summarizes, cut extracts fields, and less lets me inspect.
```

## After reading: concept questions

Answer without looking back:

1. Why is `grep` powerful in pipelines?
2. What does it mean that `grep` reads stdin by default?
3. Why should he master core `grep` before installing alternatives?
4. When might `ripgrep`, `ack`, or `ag` become worth investigating?
5. Why is portability important for sysadmin scripts or instructions?
6. How does this chapter extend TLCL Chapters 19 and 20?

---

# Exercise 1: Count matching lines

## Skill being gained

Use grep to select rows, then TLCL Ch. 20 tools to count.

## Do not type yet

Question:

```text
How many WARN or ERROR lines are in app.log?
```

Design:

```text
grep selects WARN or ERROR lines.
wc -l counts them.
```

Predict the command before typing.

## Run

```bash
grep -E ' WARN | ERROR ' app.log

grep -E ' WARN | ERROR ' app.log | wc -l
```

## Explain-back

Answer:

1. What did grep output before `wc`?
2. What did `wc -l` count?
3. Why is it better to inspect before counting?
4. Could false positives affect the count?

---

# Exercise 2: Summarize status levels

## Skill being gained

Combine grep with field extraction and summarizing.

## Predict before typing

Look at `app.log` and answer:

```text
Which field contains INFO/WARN/ERROR/error?
What tool can print that field?
What tool can summarize repeated values?
```

## Run

```bash
awk '{ print $2 }' app.log

awk '{ print $2 }' app.log | sort | uniq -c
```

Now compare with grep-first:

```bash
grep -i error app.log | awk '{ print $2 }' | sort | uniq -c
```

## Explain-back

Answer:

1. Why did we use `awk` after grep?
2. What did `sort | uniq -c` do?
3. Is this more TLCL Ch. 20 than Kerr Ch. 5?
4. Why is combining them powerful?

---

# Exercise 3: Use grep with less

## Skill being gained

Inspect search results without flooding the terminal.

## Predict before typing

Answer:

```text
Why might grep output be too long?
Why is less useful after grep?
What could I search inside less?
```

## Run

```bash
grep -R -Hn -C 2 -i error logs | less
```

Inside `less`, try:

```text
/error
n
q
```

If `less` is inconvenient in the current environment, run without `less` and explain what `less` would add.

## Explain-back

Answer:

1. What did `-C 2` add?
2. Why did `less` help?
3. What does `/pattern` do inside `less`?
4. Why is this a realistic troubleshooting pattern?

---

# Exercise 4: Know when not to use grep

## Skill being gained

Avoid using grep when another tool is clearer.

For each task, choose the better first tool:

| Task | Better first tool | Why |
|---|---|---|
| Find files by filename | `find` | grep searches text; find searches filenames/metadata |
| Select rows containing text | `grep` | grep is a line filter |
| Extract the third field | `awk` or `cut` | grep does not understand fields well |
| Replace text | `sed` | grep does not edit or transform text |
| Count matching lines | `grep ... | wc -l` or `grep -c` | either is acceptable, but inspect first |
| Search huge code trees interactively | `grep` first; later maybe `ripgrep` | master portable core first |

Explain each choice in your own words.

---

# Final lab: investigate a log problem

## Goal

Answer this question:

```text
Which app has error-like events, where are they, and what context surrounds them?
```

## Step 1: Write the investigation plan before typing

Fill in:

```text
Question:
Files/directories to search:
Pattern:
Case-sensitive or case-insensitive:
Need filenames? yes/no
Need line numbers? yes/no
Need context? yes/no
Possible false positives:
```

## Step 2: Broad search

```bash
grep -R -Hn -i err logs
```

Inspect the result.

## Step 3: Improve precision

Try:

```bash
grep -R -Hn -E ' ERROR | error ' logs
```

Then try context:

```bash
grep -R -Hn -C 2 -E ' ERROR | error ' logs
```

## Step 4: Summarize

```bash
grep -R -Hn -E ' ERROR | error ' logs | wc -l
```

Then write a short report:

```text
Problem searched:
Command used:
Files with matches:
Number of matching lines:
Most important evidence:
False positives checked:
Next command I would run:
```

---

# Day 4 finish standard

He is done only if he can say:

```text
grep is a line selector.
Regex makes grep more precise.
Context flags make grep useful for investigations.
-Hn makes evidence traceable.
-v removes noise but can hide important lines.
grep works well with TLCL Ch. 20 tools like wc, sort, uniq, cut, awk, and less.
I should master portable grep before depending on alternatives.
```
