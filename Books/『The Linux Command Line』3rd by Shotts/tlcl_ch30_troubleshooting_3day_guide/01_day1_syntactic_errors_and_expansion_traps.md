# TLCL Chapter 30: Troubleshooting

This guide is for Chapter 30, **Troubleshooting**, from William Shotts's *The Linux Command Line*.

The goal is not to memorize debugging tricks.

The goal is to build this habit:

```text
Observe the failure.
State what should have happened.
Make one hypothesis.
Use one test.
Inspect evidence.
Change one thing.
Retest.
Explain what happened.
```

Feynman analogy:

```text
Debugging is like fixing a lamp.
A careless person shakes it and hopes it works.
A disciplined thinker asks:
Is there power? Is the bulb good? Is the switch working? Is the wire broken?
One test at a time.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
```

Disciplined rule:

```text
Do not randomly edit a broken script.
Write down what you expected, what actually happened, and what evidence you used.
```

---
# Day 1: Syntactic Errors and Expansion Traps

## Read before exercises

Read Chapter 30 from the beginning through:

```text
Syntactic Errors
Missing Quotes
Missing or Unexpected Tokens
Unanticipated Expansions
Logical Errors
```

## What he should gain from this reading

He should gain the ability to classify failures.

```text
Syntax error = Bash cannot understand the script.
Expansion trap = Bash understood the script, but expanded something differently than expected.
Logical error = the script runs, but the thinking is wrong.
```

This is the first discipline:

```text
Name the type of problem before trying to fix it.
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
A syntax error is like an English sentence with broken grammar.
A logical error is like a grammatically correct sentence that gives bad instructions.
An expansion error is like giving instructions to a translator who changes words before the worker sees them.
```

Example:

```bash
rm $target/*
```

Before `rm` runs, the shell expands `$target` and `*`.

So the real question is:

```text
What command did rm actually receive?
```

---

# After reading: concept questions

Answer without looking back:

1. What is a syntactic error?
2. Why will Bash usually stop when it finds a syntax error?
3. Why are missing quotes hard to diagnose?
4. What is an unexpected token?
5. What does “unanticipated expansion” mean?
6. Why can an unquoted variable become multiple words?
7. Why can a glob pattern become many filenames?
8. What is a logical error?
9. Why is a logical error often more dangerous than a syntax error?
10. Why should he classify the bug before editing the script?

Do not touch the keyboard until these are answered.

---

# Exercise 1: Build a broken script on purpose

## Skill being gained

He is learning that an error message is evidence, not noise.

## Predict before typing

Before creating the script, answer:

```text
What kind of script is this supposed to be?
What output should it create if correct?
Where might syntax errors happen?
```

## Create the script

```bash
cd ~/tlcl-ch30-troubleshooting

cat > trouble1.sh <<'EOF'
#!/usr/bin/env bash

# trouble1.sh - intentionally broken script

name="linuxbox

if [[ -n "$name" ]]; then
    echo "Hello, $name"
fi
EOF
```

Run a syntax check first:

```bash
bash -n trouble1.sh
```

Then run it:

```bash
bash trouble1.sh
```

## Debugging log

Fill this out:

```text
Expected behavior:
Actual behavior:
Error message:
Bug type:
Smallest suspicious area:
Hypothesis:
Test:
Fix:
Retest result:
Feynman explanation:
```

## Fix

Fix only the missing quote:

```bash
name="linuxbox"
```

Run again:

```bash
bash -n trouble1.sh
bash trouble1.sh
```

## Explain-back

Answer:

1. Why did `bash -n` help?
2. Did Bash execute the script during `bash -n`?
3. What was the smallest fix?
4. Why would random editing be worse?

---

# Exercise 2: Unexpected token

## Skill being gained

He is learning to inspect structure: every `if` must be closed, every block must have its matching end.

## Predict before typing

Answer:

```text
What structure does an if statement require?
What happens if fi is missing?
```

## Create broken script

```bash
cat > trouble2.sh <<'EOF'
#!/usr/bin/env bash

file="/etc/passwd"

if [[ -f "$file" ]]; then
    echo "$file exists"
EOF
```

Check:

```bash
bash -n trouble2.sh
bash trouble2.sh
```

## Fix

Add:

```bash
fi
```

Then retest:

```bash
bash -n trouble2.sh
bash trouble2.sh
```

## Explain-back

Answer:

1. What token was missing?
2. How did Bash describe the problem?
3. Why might the line number not always point exactly at the real mistake?
4. What habit prevents this problem?

---

# Exercise 3: Unanticipated expansion

## Skill being gained

He is learning that unquoted variables can split into multiple words.

## Predict before typing

Before running, predict both outputs:

```text
What happens when the variable is unquoted?
What happens when the variable is quoted?
```

## Create test script

```bash
cat > expansion-test.sh <<'EOF'
#!/usr/bin/env bash

filename="my report.txt"

echo "Unquoted:"
printf '<%s>
' $filename

echo "Quoted:"
printf '<%s>
' "$filename"
EOF

bash expansion-test.sh
```

## Explain-back

Answer:

1. How many arguments did `printf` receive in the unquoted case?
2. How many arguments did it receive in the quoted case?
3. Why does this matter for filenames?
4. What rule should he follow for variable expansion?

Rule:

```text
When expanding a variable that may contain spaces or special characters, quote it unless there is a specific reason not to.
```

---

# Exercise 4: Glob expansion before the command runs

## Skill being gained

He is learning that the shell changes glob patterns before the command receives them.

## Setup

```bash
mkdir -p glob-lab
cd glob-lab
: > a.txt
: > b.txt
: > notes.md
cd ..
```

## Predict before typing

Answer:

```text
What will *.txt become before printf runs?
What will "*.txt" become?
```

## Run

```bash
cd glob-lab
printf '<%s>
' *.txt
printf '<%s>
' "*.txt"
cd ..
```

## Explain-back

Answer:

1. Which command received filenames?
2. Which command received literal `*.txt`?
3. Why is this important before using `rm`, `mv`, `cp`, or `chmod`?

---

# Day 1 finish standard

He is done only if he can say:

```text
I can tell whether a bug is syntax, expansion, or logic.
I can use bash -n before running.
I can explain why missing quotes break scripts.
I can show what arguments a command receives using printf '<%s>
'.
I will not edit randomly before identifying evidence.
```
