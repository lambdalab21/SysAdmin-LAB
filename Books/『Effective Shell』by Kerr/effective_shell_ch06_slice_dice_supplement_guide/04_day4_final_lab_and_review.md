#Book/Effective-Shell #Author/Kerr
# Day 4: Final Lab and Review

## Read before exercises

Reread Kerr Ch. 6 Summary.

Then skim your TLCL notes from:

```text
Ch. 19 Regular Expressions
Ch. 20 Text Processing
```

## What he should gain

He should integrate TLCL and Kerr:

```text
Regex helps select lines precisely.
Text tools reshape selected text.
Pipelines connect small transformations.
Inspection prevents false confidence.
```

---

# Warm-up: answer before typing

Answer from memory:

1. Difference between `head` and `tail`?
2. Difference between `tr` and `sed`?
3. Difference between `cut -d',' -f 3` and `cut -c 3`?
4. Why does `uniq` usually need `sort` before it?
5. What does `less` add to the workflow?
6. How does TLCL Ch. 19 regex connect to Kerr Ch. 6?
7. How does TLCL Ch. 20 text processing connect to Kerr Ch. 6?

---

# Final Lab 1: Movie report

## Question

```text
From movies.csv, produce a sorted list of movie titles without the header.
```

## Do not type yet

Write the plan:

```text
Input:
Header problem:
Field needed:
Delimiter:
Sorting needed:
Verification command:
```

## Build stage by stage

```bash
cd ~/effective-shell-ch06-practice
head movies.csv
```

```bash
tail -n +2 movies.csv
```

```bash
tail -n +2 movies.csv | cut -d',' -f 3
```

```bash
tail -n +2 movies.csv | cut -d',' -f 3 | sort
```

Save it:

```bash
tail -n +2 movies.csv | cut -d',' -f 3 | sort > movie-titles.txt
cat movie-titles.txt
```

## Explain-back

Explain every stage:

```text
tail -n +2:
cut -d',' -f 3:
sort:
> movie-titles.txt:
```

---

# Final Lab 2: Error summary

## Question

```text
Which distinct error messages appear in app.log, and how many times does each occur?
```

## Do not type yet

Write the plan:

```text
Line-selection tool:
Pattern:
Text-removal tool:
Why sort is needed:
Why uniq -c is useful:
```

## Build stage by stage

```bash
grep 'error' app.log
```

```bash
grep 'error' app.log | cut -c 22-
```

```bash
grep 'error' app.log | cut -c 22- | sort
```

```bash
grep 'error' app.log | cut -c 22- | sort | uniq -c
```

## Improve the question

Now answer:

```text
Is matching lowercase error enough?
What if logs contain ERROR, Error, or err?
Should I use grep -i?
```

Try:

```bash
grep -i 'error' app.log | cut -c 22- | sort | uniq -c
```

## Explain-back

1. What did `grep` select?
2. What did `cut` remove?
3. Why did `sort` come before `uniq`?
4. What did `uniq -c` show?
5. What false matches might exist?

---

# Final Lab 3: Pod status extraction

## Question

```text
From pods.txt, show only pod names, without the header.
```

## Warning

This file is aligned by spaces. Multiple spaces make `cut -d' ' -f 1` fragile.

This is an important lesson: sometimes `awk` is better than `cut` for whitespace-separated columns.

## Try Kerr-style cut first

Predict:

```text
Will cut -d' ' -f 1 work cleanly with multiple spaces?
```

Run:

```bash
cut -d' ' -f 1 pods.txt
```

Now remove header:

```bash
tail -n +2 pods.txt | cut -d' ' -f 1
```

## Now compare with `awk`

```bash
tail -n +2 pods.txt | awk '{ print $1 }'
```

## Explain-back

Answer:

1. Did `cut` work here?
2. Why can multiple spaces make `cut` awkward?
3. Why is `awk '{ print $1 }'` often better for whitespace-separated columns?
4. What did this teach about choosing tools?

---

# Review quiz

Answer without notes.

## Commands

1. Show first 5 lines of `file.txt`.
2. Show last 20 lines of `app.log`.
3. Show everything from line 2 onward.
4. Replace commas with newlines.
5. Delete double quotes.
6. Extract field 3 from comma-separated text.
7. Extract characters 12 through 19.
8. Sort lines in reverse order.
9. Show unique lines with counts.
10. Page through sorted output with `less`.

## Concepts

1. Why build pipelines one stage at a time?
2. Why inspect with `head` before using `cut`?
3. Why is `tr` not for replacing words?
4. Why does `uniq` not remove all duplicates unless sorted first?
5. Why is `less` part of effective shell work?
6. When should `awk` be preferred over `cut`?

---

# Retention drills

Do these one day later and one week later.

## Drill A

```text
From movies.csv, show only ratings, sorted from high to low.
```

Hint: `sort -nr` may be useful.

## Drill B

```text
From app.log, show only request paths.
```

Hint: inspect first; do not guess.

## Drill C

```text
From pods.txt, count how many pods are Running.
```

Hint: line selection + count.

Possible tools:

```bash
grep
wc -l
awk
```

---

# Final standard

He is finished with this supplement when he can say:

```text
I know how to inspect text with head, tail, and less.
I know how to transform simple characters with tr.
I know how to extract fields and character ranges with cut.
I know why sort often comes before uniq.
I know when cut is too weak and awk is better.
I build pipelines one stage at a time and verify each stage.
```
