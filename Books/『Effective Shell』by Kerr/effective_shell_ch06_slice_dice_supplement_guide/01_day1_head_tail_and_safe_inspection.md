#Book/Effective-Shell #Author/Kerr
#bash/command/tail #bash/command/safe

# Day 1: `head`, `tail`, and Safe Inspection

## Read before exercises

Read Kerr Ch. 6 from the beginning through:

```text
Heads and Tails
```

## What he should gain

He should gain the habit of **inspecting text before transforming it**.

Feynman analogy:

```text
A mechanic does not start replacing parts before opening the hood.
A shell user should not start cutting, grepping, or sorting before looking at the text.
```

`head` and `tail` are not boring. They are the first step in disciplined text work.

---

# After reading: concept questions

Answer without looking back:

1. What does `head` show by default?
2. What does `tail` show by default?
3. What does `head -n 3 file` mean?
4. What does `tail -n 3 file` mean?
5. Why is `tail` useful for logs?
6. What does `tail -f` do?
7. What does `tail -n +2` mean?
8. Why is previewing a file before transforming it a disciplined habit?

Do not start exercises until these are answered.

---

# Exercise 1: Inspect before transforming

## Skill being gained

He is learning that the first command in a pipeline is often just inspection.

## Do not type yet

Predict:

```text
What will the first line of movies.csv be?
What kind of data does each line contain?
Which line is a header?
```

## Run

```bash
cd ~/effective-shell-ch06-practice
head movies.csv
```

Then:

```bash
head -n 3 movies.csv
```

## Explain-back

Answer:

1. Why is `head movies.csv` useful before `cut`?
2. What delimiter appears to separate the fields?
3. Which field seems to contain the title?
4. What could go wrong if you guessed the delimiter without inspecting?

---

# Exercise 2: Inspect the end

## Skill being gained

He is learning when the last lines matter.

## Predict before typing

```text
What will tail show that head did not show?
Would tail be more useful for a CSV file or for a log file? Why?
```

## Run

```bash
tail movies.csv
```

Then:

```bash
tail app.log
```

## Explain-back

Answer:

1. Why might `tail` be more useful than `head` for logs?
2. What is the newest-looking event in `app.log`?
3. What kind of question could `tail app.log` answer quickly?

---

# Exercise 3: Strip a header with `tail -n +2`

## Skill being gained

He is learning a practical trick: remove the header row before processing data.

## Read again before exercise

Reread the part where Kerr uses:

```bash
tail -n +2
```

## Predict before typing

```text
If movies.csv has a header on line 1, what should tail -n +2 show?
Should it show line 2 only, or line 2 through the end?
```

## Run

```bash
tail -n +2 movies.csv
```

Then compare:

```bash
head -n 3 movies.csv
head -n 3 movies.csv | tail -n +2
```

## Explain-back

Answer:

1. What does the `+` mean in `tail -n +2`?
2. Why is this different from `tail -n 2`?
3. When would you use this before `cut`, `sort`, or `uniq`?

---

# Exercise 4: Build only one pipeline stage at a time

## Skill being gained

He is learning to avoid mindless pipelines.

## Predict

Question:

```text
How can I see only the first few data rows, without the header?
```

Plan:

```text
Stage 1: show first 5 lines
Stage 2: remove line 1 from that output
```

## Run stage by stage

```bash
head -n 5 movies.csv
```

Inspect it.

Then:

```bash
head -n 5 movies.csv | tail -n +2
```

## Explain-back

Answer:

1. What did stage 1 do?
2. What did stage 2 do?
3. Why is this safer than writing a long pipeline immediately?

---

# Day 1 finish standard

He is done only if he can say:

```text
I inspect text before transforming it.
head and tail help me see structure.
tail -n +2 means from line 2 onward.
I build pipelines one stage at a time.
```
