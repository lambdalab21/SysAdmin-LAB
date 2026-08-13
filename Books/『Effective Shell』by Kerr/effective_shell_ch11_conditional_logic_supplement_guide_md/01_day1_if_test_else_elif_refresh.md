# Day 1: `if`, `test`, `else`, `elif`, and Ordered Thinking

## Read before exercises

Read Kerr Ch. 11 from the beginning through these sections:

```text
The If Statement
The Test Command
Using Multiple Statements in a Single Line
The Else Statement
The Elif Statement
Common Test Operators
Common Test Operators for Files
```

## What he should gain

He should refresh this idea:

```text
In shell, if usually asks whether a command succeeded.
A test is just a command whose job is to return success or failure.
```

This is the key difference from many programming languages.

```text
0      = success / true for if
nonzero = failure / false for if
```

## Feynman analogy

A shell `if` is like asking a worker:

```text
Did the command succeed?
If yes, do the next job.
If no, choose another path.
```

The worker does not return “true” or “false” as words. The worker returns a status code.

---

# After reading: concept questions

Answer before touching the keyboard.

1. In shell, why does `0` mean success?
2. What does an `if` statement test?
3. What is the job of the `test` command?
4. Why is `[ -d "$HOME" ]` really a form of `test`?
5. Why must there be spaces inside `[ ... ]`?
6. What is the difference between `-e`, `-f`, and `-d`?
7. What is the difference between `-r`, `-w`, and `-x`?
8. What is the difference between `-z "$name"` and `-n "$name"`?
9. Why can the order of `if` / `elif` checks change the result?
10. Why should a beginner write `then` on a separate line first?

Do not continue until he can answer these in plain English.

---

# Exercise 1: Exit status is the real condition

## Skill being gained

He is learning that shell conditionals are based on command success or failure.

## Predict before typing

Predict the exit status of each command:

```text
true
false
mkdir data
mkdir data again
[ -d data ]
[ -f data ]
```

## Run

```bash
cd ~/effective-shell-ch11-supplement

true
echo $?

false
echo $?

mkdir -p data
echo $?

mkdir data
echo $?

[ -d data ]
echo $?

[ -f data ]
echo $?
```

## Explain-back

Answer:

1. Which commands succeeded?
2. Which commands failed?
3. Why did `mkdir data` behave differently the second time?
4. Why does `[ -d data ]` succeed but `[ -f data ]` fail?
5. What would an `if` statement do with each result?

---

# Exercise 2: Write `if` as an English decision first

## Skill being gained

He is learning to design the condition before writing syntax.

## Read before exercise

Reread Kerr’s `if` and `test` sections.

## Do not type yet

Fill this out:

```text
Decision:
Condition in English:
Command/test:
If true, script should:
If false, script should:
How I will test both cases:
```

Use this decision:

```text
If the local bin directory does not exist, create it.
```

## Write the script

```bash
cat > check-bin.sh <<'EOF'
#!/usr/bin/env bash

lab_bin="$HOME/effective-shell-ch11-supplement/bin"

if [ ! -d "$lab_bin" ]
then
    echo "Creating local bin directory: $lab_bin"
    mkdir -p "$lab_bin"
else
    echo "Local bin directory already exists: $lab_bin"
fi
EOF

bash check-bin.sh
bash check-bin.sh
```

## Explain-back

1. Why is `$lab_bin` quoted?
2. What does `!` do?
3. Why did the output change, or not change, on the second run?
4. What branch ran first?
5. What branch ran second?

---

# Exercise 3: Test file states in the right order

## Skill being gained

He is learning that `elif` order matters.

## Read before exercise

Reread Kerr’s `elif` section.

Pay attention to the warning about checking a broader condition before a narrower one.

## Setup

```bash
cd ~/effective-shell-ch11-supplement
cat > bin/common <<'EOF'
#!/usr/bin/env bash
echo "common command placeholder"
EOF
chmod -x bin/common
```

## Predict before typing

For each test, predict true or false:

```bash
[ -e bin/common ]
[ -f bin/common ]
[ -x bin/common ]
```

Then run:

```bash
[ -e bin/common ]; echo "-e status: $?"
[ -f bin/common ]; echo "-f status: $?"
[ -x bin/common ]; echo "-x status: $?"
```

## Write a correctly ordered check

```bash
cat > check-common.sh <<'EOF'
#!/usr/bin/env bash

common_path="$HOME/effective-shell-ch11-supplement/bin/common"

if [ -x "$common_path" ]; then
    echo "common exists and is executable."
elif [ -e "$common_path" ]; then
    echo "common exists but is not executable."
else
    echo "common is not installed."
fi
EOF

bash check-common.sh
chmod +x bin/common
bash check-common.sh
rm -f bin/common
bash check-common.sh
```

## Explain-back

1. Why do we check `-x` before `-e`?
2. What would go wrong if `-e` came first?
3. How did you force all three branches to run?
4. What does this teach about testing scripts?

---

# Exercise 4: Multiple statements on one line, but only after understanding

## Skill being gained

He is learning to read compact shell syntax without rushing to write it.

## Read before exercise

Reread Kerr’s section on multiple statements in a single line.

## Explain before typing

What is the purpose of `;` here?

```bash
if [ -d data ]; then echo "data exists"; fi
```

## Run both forms

Readable form:

```bash
if [ -d data ]
then
    echo "data exists"
fi
```

Compact form:

```bash
if [ -d data ]; then echo "data exists"; fi
```

## Explain-back

1. Which form is better for learning?
2. Which form might be useful interactively?
3. Why is the semicolon needed before `then` in the compact form?
4. Why should a beginner prefer the multi-line version inside scripts?

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
Shell if statements test command success.
The test command returns success or failure.
[ ... ] is test syntax, not decoration.
File tests must be chosen carefully.
elif order matters because the first matching branch wins.
I can test every branch deliberately.
```
