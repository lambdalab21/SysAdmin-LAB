# Shotts Chapter 27: Flow Control — Branching with `if`

This guide is for Chapter 27 of William Shotts's *The Linux Command Line*: **Flow Control: Branching with if**.

The purpose is not to memorize `if` syntax.

The purpose is to learn disciplined branching:

```text
What condition am I testing?
What counts as success?
What should happen if it succeeds?
What should happen if it fails?
How will I prove the script chose the right branch?
```

Feynman analogy:

```text
An if statement is a fork in the road.
The test is the road sign.
The script should not guess which road to take.
It should inspect evidence, then choose.
```

Main continuing project:

```text
lab-report.sh
```

The script will grow from the Chapter 25–26 report project into a report that changes behavior based on conditions.

Create a safe working directory:

```bash
mkdir -p ~/tlcl-ch27-if
cd ~/tlcl-ch27-if
```

If you already have `lab-report.sh` from Chapter 26, copy it here. If not, the Day 4 final lab creates a complete version.

Core discipline:

```text
Before typing: predict the branch.
After running: verify the branch.
Before moving on: explain why Bash chose that branch.
```

---
# Day 3: Modern Tests with `[[ ]]` and Arithmetic Decisions with `(( ))`

## Read before exercises

Read these Chapter 27 sections:

```text
A More Modern Version of test
(( ))—Designed for Integers
```

## What he should gain from this reading

He should gain this idea:

```text
Use the right testing form for the job.
```

Useful rule of thumb:

```text
[[ ]]  = preferred Bash conditional expression for files and strings
(( ))  = preferred Bash arithmetic evaluation for integers
[ ]    = older test syntax, still common and important to recognize
```

---

# Before reading: Feynman preview

Think of `[ ]`, `[[ ]]`, and `(( ))` as three workbenches:

```text
[ ]    = old general workbench, common but easy to misuse
[[ ]]  = safer Bash workbench for conditions
(( ))  = math workbench
```

A disciplined thinker asks:

```text
Am I testing a file, a string, or a number?
```

---

# After reading: concept questions

Answer without looking back:

1. How is `[[ ]]` different from `[ ]`?
2. Why is `[[ ]]` often safer in Bash scripts?
3. What kinds of tests fit naturally inside `[[ ]]`?
4. What kinds of tests fit naturally inside `(( ))`?
5. In arithmetic context, why can `count > 0` be written naturally?
6. What is the danger of confusing string comparison and integer comparison?
7. What does the right side of `==` inside `[[ ]]` sometimes behave like?
8. When should a pattern be quoted or not quoted inside `[[ ]]`?

Do not do the exercises until these are answered.

---

# Exercise 1: Convert `[ ]` tests to `[[ ]]`

## Read before this exercise

Reread:

```text
A More Modern Version of test
```

## Skill being gained

He is learning the modern Bash condition form.

## Do not type yet: predict

Answer:

```text
Will [[ -f sample.txt ]] behave like [ -f sample.txt ]?
Will [[ -d sample-dir ]] behave like [ -d sample-dir ]?
Why is [[ ]] usually less fragile with variables?
```

## Create script

```bash
cd ~/tlcl-ch27-if

mkdir -p sample-dir
echo "hello" > sample.txt

cat > modern-file-tests.sh <<'EOF'
#!/usr/bin/env bash

path="$1"

if [[ -f $path ]]; then
    echo "$path is a regular file."
else
    echo "$path is not a regular file."
fi

if [[ -d $path ]]; then
    echo "$path is a directory."
else
    echo "$path is not a directory."
fi
EOF

bash modern-file-tests.sh sample.txt
bash modern-file-tests.sh sample-dir
bash modern-file-tests.sh missing-path
```

## Read after this exercise

Reread the `[[ ]]` section and look for why Bash treats it differently from the external-looking `[ ]` test form.

## Explain-back

Answer:

1. What changed from Day 2?
2. What stayed conceptually the same?
3. Why is `[[ -f $path ]]` usually safe even without quotes?
4. Why might you still choose to quote variables for readability in some cases?

---

# Exercise 2: String pattern matching inside `[[ ]]`

## Read before this exercise

Reread the part of `A More Modern Version of test` that discusses pattern matching.

## Skill being gained

He is learning that `[[ string == pattern ]]` can use shell-style patterns.

## Important warning

Inside `[[ ]]`:

```bash
[[ $name == app* ]]
```

means:

```text
Does name start with app?
```

But:

```bash
[[ $name == "app*" ]]
```

means:

```text
Does name literally equal app*?
```

This is a place where quoting changes meaning.

## Do not type yet: predict

Predict each case:

```text
name=app01, pattern app*:
name=db01, pattern app*:
name=app*, quoted literal "app*":
```

## Create script

```bash
cat > pattern-test.sh <<'EOF'
#!/usr/bin/env bash

name="$1"

if [[ $name == app* ]]; then
    echo "$name looks like an app host."
else
    echo "$name does not look like an app host."
fi

if [[ $name == "app*" ]]; then
    echo "$name literally equals app*."
else
    echo "$name does not literally equal app*."
fi
EOF

bash pattern-test.sh app01
bash pattern-test.sh db01
bash pattern-test.sh 'app*'
```

## Read after this exercise

Reread the same `[[ ]]` section and write one sentence explaining why quoting can change pattern behavior.

## Explain-back

Answer:

1. Which test used a pattern?
2. Which test used a literal string?
3. Is `app*` here a regex or a shell pattern?
4. Why is this related to globbing from Shotts Chapter 7?
5. Why must he not confuse this with `grep 'app.*'`?

---

# Exercise 3: Arithmetic decisions with `(( ))`

## Read before this exercise

Reread:

```text
(( ))—Designed for Integers
```

## Skill being gained

He is learning to use arithmetic syntax for numeric decisions.

## Do not type yet: predict

For each value, predict the branch:

```text
count=0:
count=1:
count=5:
```

## Create script

```bash
cat > arithmetic-if.sh <<'EOF'
#!/usr/bin/env bash

count="$1"

if (( count > 0 )); then
    echo "count is positive."
else
    echo "count is zero or negative."
fi
EOF

bash arithmetic-if.sh 0
bash arithmetic-if.sh 1
bash arithmetic-if.sh 5
bash arithmetic-if.sh -2
```

## Read after this exercise

Reread the `(( ))` section and identify which operators look familiar from other programming languages.

## Explain-back

Answer:

1. Why is `(( count > 0 ))` clearer than `[ "$count" -gt 0 ]` for many programmers?
2. Why did the script not need `$count` inside `(( ))`?
3. What does Bash assume inside arithmetic context?
4. What would happen if `count` were empty or nonnumeric?
5. Why is input validation still important?

---

# Exercise 4: Choose the right testing form

## Read before this exercise

Reread both sections:

```text
A More Modern Version of test
(( ))—Designed for Integers
```

## Skill being gained

He is learning to choose, not merely copy syntax.

## For each condition, choose `[ ]`, `[[ ]]`, or `(( ))`

Fill in the table before running anything:

| Condition | Best form | Why? |
|---|---|---|
| file exists and is regular |  |  |
| hostname starts with `app` |  |  |
| count is greater than 10 |  |  |
| variable is empty |  |  |
| path is readable |  |  |
| number of errors equals zero |  |  |

## Create script

```bash
cat > choose-test-form.sh <<'EOF'
#!/usr/bin/env bash

path="sample.txt"
host="app01"
count=12
name=""
errors=0

if [[ -f $path ]]; then
    echo "file check: yes"
fi

if [[ $host == app* ]]; then
    echo "host pattern check: yes"
fi

if (( count > 10 )); then
    echo "count check: yes"
fi

if [[ -z $name ]]; then
    echo "empty string check: yes"
fi

if [[ -r $path ]]; then
    echo "readable path check: yes"
fi

if (( errors == 0 )); then
    echo "error count check: yes"
fi
EOF

bash choose-test-form.sh
```

## Read after this exercise

Reread the two section titles and explain why Shotts separates modern tests from arithmetic tests.

## Explain-back

Answer:

1. Which tests were file tests?
2. Which tests were string tests?
3. Which tests were arithmetic tests?
4. Which form did you choose for each?
5. Why is “what kind of data am I testing?” the first question?

---

# Day 3 finish standard

He is done only if he can explain:

```text
[[ ]] is usually the better Bash conditional form for files and strings.
(( )) is designed for integer arithmetic decisions.
The right side of == in [[ ]] can be a shell pattern if unquoted.
Regex, glob patterns, and arithmetic comparisons are different tools.
```
