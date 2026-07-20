# Day 3 — Capture Groups, Templates, and Safe Editing

## Read before exercises

Read these Kerr Ch. 7 sections:

```text
Extracting Information
Advanced - Surrounding Parts of Text with Quotes
Advanced - Template Files
What About 'In Place' Editing?
What about Awk?
When to Program
Summary
```

## What he should gain

He should gain this ability:

```text
Use capture groups to extract or rearrange parts of text, and know when not to force sed to do too much.
```

This day adds techniques that may go beyond basic TLCL use.

Feynman analogy:

```text
A capture group is like putting a sticky note on part of a sentence:
“This part is the key.”
“This part is the separator.”
“This part is the value.”
Then the replacement can rebuild the sentence using those sticky notes.
```

---

# Before reading: memory refresh

Answer before reading:

1. What does `grep -E` mean?
2. What do parentheses do in extended regex?
3. What does `[0-9]+` mean?
4. What does `[^"]+` mean?
5. Why is it useful to test a regex with `grep` before using it inside `sed`?

---

# After reading: concept questions

Answer without looking back:

1. What is a capture group?
2. In `sed -E`, how do you create a capture group?
3. What does `\1` mean in the replacement side?
4. What does `\2` mean?
5. Why is `sed -n 's/pattern/replacement/p'` useful while testing?
6. Why can template placeholders such as `%NAME%` be easier than complex regex?
7. Why is `sed -i` risky or non-portable across systems?
8. When should you stop using `sed` and consider `awk` or a small program?

---

# Setup

Use the same directory:

```bash
cd ~/effective-shell-ch07-sed
```

Create a small CSV-like file:

```bash
cat > movies.txt <<'EOF'
Rank,Rating,Title,Reviews
1,97,Black Panther (2018),515
2,94,Avengers: Endgame (2019),531
3,93,Us (2019),520
EOF
```

Create a small YAML-like file:

```bash
cat > page.md <<'EOF'
---
title: "Advanced Text Manipulation"
slug: advanced-text-manipulation
weight: 14
---

# Notes
Some ordinary text: not a metadata line.
EOF
```

Create a template:

```bash
cat > service.template.conf <<'EOF'
service=%SERVICE_NAME%
environment=%ENVIRONMENT%
port=%PORT%
EOF
```

---

# Exercise 1: Extract years with capture groups

## Discipline gate

Before typing, write:

```text
What part of each movie line do I want?
What surrounds that part?
What lines should not be transformed?
What false matches are possible?
```

## Build the pattern with grep first

```bash
grep -E '\([0-9]+\)' movies.txt
```

Explain:

```text
\( matches a literal opening parenthesis.
[0-9]+ matches one or more digits.
\) matches a literal closing parenthesis.
```

## Use sed to keep only the captured year

Predict before running:

```text
What will happen to the header line?
```

Run:

```bash
sed -E 's/.*\(([0-9]+)\).*/\1/' movies.txt
```

Now skip the first line:

```bash
sed -E -e '1d' -e 's/.*\(([0-9]+)\).*/\1/' movies.txt
```

Answer:

1. What did `1d` do?
2. What did the capture group capture?
3. Why did the replacement use `\1`?
4. Why is this fragile for real CSV files?

---

# Exercise 2: Match metadata lines safely

## Goal

Find lines like:

```text
slug: advanced-text-manipulation
weight: 14
```

but avoid ordinary prose containing colons.

## Start too broad

Predict what this might match:

```bash
grep -E '[^:]+:' page.md
```

Run it:

```bash
grep -E '[^:]+:' page.md
```

Answer:

1. Did it match too much?
2. Why?
3. What does `[^:]+:` actually mean?

## Improve the pattern

Run:

```bash
grep -E '^[^: "-]+:' page.md
```

Explain every symbol:

```text
^:
[^: "-]+:
::
```

---

# Exercise 3: Quote unquoted metadata values

## Read before exercise

Reread the section on surrounding parts of text with quotes.

## Build in inspect mode

First print only lines with unquoted values:

```bash
sed -E -n '/^[^: "-]+: +[^"-][^\"]*$/p' page.md
```

This pattern is intentionally a little conservative for practice. If it misses something, explain why.

Now separate key, separator, and value:

```bash
sed -E -n 's/(^[^: "-]+)(: +)([^"-][^"]*$)/Key="\1" Separator="\2" Value="\3"/p' page.md
```

Finally quote the value:

```bash
sed -E 's/(^[^: "-]+)(: +)([^"-][^"]*$)/\1\2"\3"/' page.md > page.updated.md
```

Inspect:

```bash
cat page.updated.md
```

Answer:

1. What did group 1 capture?
2. What did group 2 capture?
3. What did group 3 capture?
4. Why did we write to a new file?
5. What lines were intentionally unchanged?

---

# Exercise 4: Simple template replacement

## Skill

Use obvious placeholders instead of complex patterns.

Predict:

```text
Which placeholders exist?
What values should replace them?
Should I use single quotes or double quotes if shell variables must expand?
```

Set variables:

```bash
SERVICE_NAME=inventory
ENVIRONMENT=dev
PORT=8080
```

Run:

```bash
sed -e "s/%SERVICE_NAME%/${SERVICE_NAME}/" \
    -e "s/%ENVIRONMENT%/${ENVIRONMENT}/" \
    -e "s/%PORT%/${PORT}/" \
    service.template.conf > service.conf
```

Inspect:

```bash
cat service.conf
```

Explain:

1. Why were double quotes used around the sed expressions?
2. What could go wrong if a replacement value contains `/` or `&`?
3. Why are obvious placeholders like `%PORT%` easier to reason about?

---

# Exercise 5: Safe editing instead of careless `sed -i`

## Read before exercise

Reread “What About 'In Place' Editing?”

## Skill

Learn the safe edit pattern:

```text
input file
→ sed output to temporary file
→ inspect temporary file
→ move temporary file over original only when correct
```

Run:

```bash
cp service.conf service.conf.backup
sed 's/environment=dev/environment=staging/' service.conf > service.conf.tmp
cat service.conf.tmp
mv service.conf.tmp service.conf
cat service.conf
```

Answer:

1. Why is this safer than immediately editing in place?
2. Why keep a backup during practice?
3. What should you inspect before `mv`?
4. Why does `sed -i` differ between GNU/Linux and macOS/BSD examples?

---

# Exercise 6: When not to use sed

For each task, choose `grep`, `sed`, `awk`, or Python/small program.

| Task | Best first tool | Reason |
|---|---|---|
| Show lines containing ERROR |  |  |
| Replace `app01` with `app02` in a config preview |  |  |
| Print the third colon-separated field |  |  |
| Sum numbers from a column |  |  |
| Parse real CSV with quoted commas |  |  |
| Change a few obvious placeholders in a template |  |  |

Expected thinking:

```text
grep = select lines
sed = simple line transformations
awk = fields and simple reports
program = complex parsing, real data structures, maintainability
```

---

# Day 3 finish standard

He is done only if he can explain:

```text
Capture groups save parts of a match.
Backreferences reuse captured parts in the replacement.
sed -n with /p is useful for testing.
Template placeholders are often clearer than clever regex.
sed -i should be used cautiously.
If the sed expression becomes hard to explain, awk or a small program may be better.
```
