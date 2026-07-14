#Book/Effective-Shell #Author/Kerr
# Day 2: Patterns, Precision, and False Matches

## Read before exercises

Read Effective Shell Chapter 5 section:

```text
Using Patterns
```

Review TLCL Chapter 19:

```text
^ and $
[] and [^]
[[:digit:]], [[:alpha:]], [[:space:]]
Basic regex vs extended regex
grep vs grep -E
```

## What he should gain

He should gain this idea:

```text
The value of grep depends on pattern precision.
A sloppy pattern gives misleading evidence.
```

He is not just learning syntax.
He is learning investigation discipline.

## Before reading: Feynman preview

Explain this before reading:

```text
A regex is a rule.
If the rule is vague, the evidence is noisy.
If the rule is too narrow, it misses evidence.
```

## After reading: concept questions

Answer without looking back:

1. Why did a simple search for `man` match words like `performance` or `command`?
2. Why are anchors useful?
3. What is the difference between basic regex and extended regex in `grep`?
4. Why does `grep -E` often feel more familiar?
5. What is a false positive?
6. What is a false negative?
7. Why should he test both positive and negative examples?

---

# Exercise 1: Contains vs starts with

## Skill being gained

Use anchors to make the question more precise.

## Do not type yet

Predict:

```text
grep error app.log
    should match lines containing lowercase error anywhere.

grep '^2026.*ERROR' app.log
    should match lines where ERROR appears after the timestamp on the line.
```

## Run

```bash
grep error app.log

grep '^2026.*ERROR' app.log
```

## Explain-back

Answer:

1. Which search was broader?
2. Which search was more precise?
3. Did either miss lowercase `error`?
4. How could you include both uppercase and lowercase error?

Try:

```bash
grep -i '^2026.*error' app.log
```

---

# Exercise 2: Basic regex vs extended regex

## Skill being gained

Choose `grep` or `grep -E` deliberately.

## Read before exercise

Reread Kerr’s explanation of basic regular expressions and `grep -E`.

## Predict before typing

Answer:

```text
Which form treats + as a repetition operator more naturally?
What should [0-9]+ mean in extended regex?
```

## Run

```bash
grep '[0-9]+' app.log

grep -E '[0-9]+' app.log
```

## Explain-back

Answer:

1. Why did plain `grep '[0-9]+'` behave differently?
2. What did `grep -E '[0-9]+'` mean?
3. Why should he not randomly add `-E` without knowing why?
4. When is `grep -E` clearer?

---

# Exercise 3: Literal brackets trap

## Skill being gained

Avoid a classic regex false-positive mistake.

## Do not type yet

Predict:

```text
grep '[installed]' packages.txt
```

Question:

```text
Does this mean match the literal string [installed]?
Or does it mean match one character from the set i,n,s,t,a,l,e,d?
```

## Run

```bash
grep '[installed]' packages.txt
```

Now run:

```bash
grep '\[installed\]' packages.txt
```

## Explain-back

Answer:

1. Why did the first command match too much?
2. What did the brackets mean in regex?
3. Why did escaping the brackets fix it?
4. What false positive did you see?

---

# Exercise 4: Search config lines precisely

## Skill being gained

Search configuration files while ignoring commented-out examples.

## Predict before typing

Question:

```text
I want active PasswordAuthentication lines, not commented-out examples.
```

Possible pattern:

```text
^[[:space:]]*PasswordAuthentication
```

Explain what every part means before running.

## Run

```bash
grep 'PasswordAuthentication' sshd_config.sample

grep -E '^[[:space:]]*PasswordAuthentication' sshd_config.sample
```

## Explain-back

Answer:

1. Which command included commented-out lines?
2. Why did `^` help?
3. Why did `[[:space:]]*` help?
4. What would happen if the directive were indented?
5. Why is this important for sysadmin work?

---

# Day 2 finish standard

He is done only if he can say:

```text
A regex is a rule for matching text.
I can explain every symbol in my pattern.
I know the difference between grep and grep -E.
I test for false positives and false negatives.
I use anchors when I mean “start of line” or “end of line.”
```
