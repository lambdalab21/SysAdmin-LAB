#Book/Effective-Shell #Author/Kerr
#bash/command/rev
# Day 3: `rev`, `sort`, `uniq`, and the Pager

## Read before exercises

Read Kerr Ch. 6 sections:

```text
A Trick with Rev
Sort and Unique
Don't Forget Your Pager!
```

## What he should gain

He should gain the habit of using small tools creatively:

```text
rev  = reverse text, sometimes useful before cut
sort = order lines
uniq = remove adjacent duplicates
less = inspect output before trusting it
```

The most important rule today:

```text
uniq only removes duplicates that are next to each other.
That is why sort often comes before uniq.
```

Feynman analogy:

```text
uniq is like checking neighboring students in a line.
If two matching students are far apart, uniq will not notice unless sort first puts them together.
```

---

# After reading: concept questions

Answer without looking back:

1. What does `rev` do?
2. Why can `rev | cut | rev` help extract text after the last delimiter?
3. What does `sort` do?
4. What does `sort -r` do?
5. What does `uniq` do?
6. Why do we often use `sort | uniq` rather than `uniq` alone?
7. When should he use `less` in a pipeline?
8. Why is inspecting intermediate output part of disciplined thinking?

---

# Exercise 1: Understand `rev`

## Skill being gained

He is learning that reversing text can turn a hard “last field” problem into an easy “first field” problem.

## Predict

```text
What will pwd print?
What will rev do to that string?
If I cut the first field after reversing, what part am I really getting?
```

## Run

```bash
cd ~/effective-shell-ch06-practice
pwd
pwd | rev
pwd | rev | cut -d'/' -f 1
pwd | rev | cut -d'/' -f 1 | rev
```

## Explain-back

Answer:

1. What did `rev` do at each stage?
2. Why did `cut -d'/' -f 1` work after reversing?
3. Could `basename "$PWD"` also solve this problem?
4. Why is it useful to know more than one method?

---

# Exercise 2: Sort movie titles

## Skill being gained

He is learning to order extracted data.

## Predict

```text
What field contains movie titles?
Should the header be removed before sorting?
What should appear near the top alphabetically?
```

## Run stage by stage

```bash
tail -n +2 movies.csv
```

Then:

```bash
tail -n +2 movies.csv | cut -d',' -f 3
```

Then:

```bash
tail -n +2 movies.csv | cut -d',' -f 3 | sort
```

Reverse sort:

```bash
tail -n +2 movies.csv | cut -d',' -f 3 | sort -r
```

## Explain-back

Answer:

1. What did each stage add?
2. Why remove the header first?
3. What does `sort -r` change?
4. Did the command answer a clear question, or was it just playing with tools?

---

# Exercise 3: `uniq` requires adjacent duplicates

## Skill being gained

He is learning the key limitation of `uniq`.

## Create duplicate data

```bash
cat > errors.txt <<'EOF'
Permission denied
Missing file
Permission denied
Timeout
Missing file
Missing file
Permission denied
EOF
```

## Predict

Before running:

```text
Will uniq remove all repeated lines?
Which duplicates are adjacent?
```

## Run

```bash
uniq errors.txt
```

Then:

```bash
sort errors.txt | uniq
```

Count duplicates:

```bash
sort errors.txt | uniq -c
```

## Explain-back

Answer:

1. Why did `uniq errors.txt` not remove all duplicates?
2. Why did `sort errors.txt | uniq` work better?
3. What does `uniq -c` add?
4. Why is this useful for log investigation?

---

# Exercise 4: Unique error messages from a log

## Skill being gained

He is learning a realistic sysadmin text pipeline.

## Question

```text
What distinct error messages appear in app.log?
```

## Do not type yet

Write the pipeline plan:

```text
Stage 1: inspect log
Stage 2: keep error lines
Stage 3: remove timestamp if useful
Stage 4: sort
Stage 5: uniq
```

## Run stage by stage

```bash
cat app.log
```

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

## Explain-back

Answer:

1. What question did the final pipeline answer?
2. Why was `grep` used first?
3. Why was `cut` used after `grep`?
4. Why did `sort` come before `uniq -c`?
5. What could be wrong with using fixed character positions in logs?

---

# Exercise 5: Use `less` as part of the workflow

## Skill being gained

He is learning that inspection is not optional when output gets large.

## Run

```bash
tail -n +2 movies.csv | cut -d',' -f 3 | sort | less
```

Inside `less`:

```text
/War
n
q
```

## Explain-back

Answer:

1. Why pipe to `less` instead of dumping everything to the terminal?
2. What does `/War` do inside `less`?
3. Why is `less` useful while building pipelines?

---

# Day 3 finish standard

He is done only if he can say:

```text
rev reverses text and can help extract from the end.
sort orders lines.
uniq only removes adjacent duplicates, so sort often comes first.
less helps me inspect large pipeline output safely.
I can build a pipeline one stage at a time and explain each stage.
```
