#Book/Effective-Shell #Author/Kerr
#bash/command/tr #bash/command/cut

# Day 2: `tr`, `cut`, and Field Extraction

## Read before exercises

Read Kerr Ch. 6 sections:

```text
Replacing Text
How to Cut
```

## What he should gain

He should gain two practical skills:

```text
tr  = transform or delete characters
cut = extract fields or character ranges
```

The important warning:

```text
tr works on characters, not words.
```

Feynman analogy:

```text
tr is like changing every red Lego brick into a blue Lego brick.
It does not understand words or sentences.
It only sees individual pieces.
```

---

# After reading: concept questions

Answer without looking back:

1. What does `tr ',' '
'` do?
2. What does `tr -d '"'` do?
3. Why does `tr 'shell' 'machine'` not replace the word `shell` with `machine`?
4. What does `cut -d',' -f 3` mean?
5. What is a delimiter?
6. What is a field?
7. What does `cut -c 12-19` mean?
8. What does `cut -d',' -f 3-` mean?
9. Why should he inspect the input before choosing `cut` options?

---

# Exercise 1: Use `tr` to split a header into lines

## Skill being gained

He is learning character transformation.

## Do not type yet

Predict:

```text
If commas become newlines, what will the header look like?
How many lines should be produced from a four-column header?
```

## Run

```bash
cd ~/effective-shell-ch06-practice
head -n 1 movies.csv
```

Then:

```bash
head -n 1 movies.csv | tr ',' '
'
```

## Explain-back

Answer:

1. What character was replaced?
2. What replaced it?
3. Did `tr` understand CSV fields, or did it simply replace characters?
4. Why is this good enough for a simple header but not a full CSV parser?

---

# Exercise 2: Delete characters with `tr -d`

## Skill being gained

He is learning simple cleanup.

## Predict

```text
What will happen if I delete parentheses from movie titles?
Will the year disappear completely?
```

## Run

```bash
cut -d',' -f 3 movies.csv | head
```

Then:

```bash
cut -d',' -f 3 movies.csv | head | tr -d '()'
```

## Explain-back

Answer:

1. Which characters were deleted?
2. Were the digits deleted?
3. Why is `tr -d '()'` character deletion, not pattern deletion?

---

# Exercise 3: Extract a field with `cut`

## Skill being gained

He is learning to extract columns from delimiter-separated text.

## Read again before exercise

Reread Kerr's explanation of:

```bash
cut -d',' -f 3
```

## Predict

```text
What is the delimiter in movies.csv?
Which field contains the title?
What will happen to the header row?
```

## Run

```bash
cut -d',' -f 3 movies.csv
```

Then remove the header:

```bash
tail -n +2 movies.csv | cut -d',' -f 3
```

## Explain-back

Answer:

1. What does `-d','` mean?
2. What does `-f 3` mean?
3. Why did the first version include `Title`?
4. How did `tail -n +2` change the result?

---

# Exercise 4: Extract character ranges from logs

## Skill being gained

He is learning that `cut` can slice by character position when the text has fixed positions.

## Predict

Look at one log line:

```bash
head -n 1 app.log
```

Before running the next command, answer:

```text
What part of the line seems to be the time?
What character positions might contain the time?
```

## Run

```bash
cut -c 12-19 app.log
```

Then:

```bash
cut -c 22- app.log
```

## Explain-back

Answer:

1. What did `cut -c 12-19` extract?
2. What did `cut -c 22-` extract?
3. Why is character cutting fragile if the format changes?
4. When is character cutting useful?

---

# Exercise 5: Choose `grep`, `cut`, or `tr`

## Skill being gained

He is learning tool selection.

For each task, choose the tool first. Do not type until the choice is explained.

| Task | Best first tool | Why? |
|---|---|---|
| Show only error lines |  |  |
| Show only movie titles |  |  |
| Remove parentheses characters |  |  |
| Show only lines after the header |  |  |
| Split a comma-separated header into one name per line |  |  |

Then test your answers with commands.

---

# Day 2 finish standard

He is done only if he can say:

```text
tr changes or deletes characters.
cut extracts fields or character ranges.
I inspect the data before choosing delimiters or character positions.
I understand that cut is useful but not a full CSV parser.
```
