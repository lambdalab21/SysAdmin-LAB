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
# Day 2: Using `test` — File, String, and Integer Checks

## Read before exercises

Read this Chapter 27 section:

```text
Using test
```

While reading, pay attention to three categories:

```text
file expressions
string expressions
integer expressions
```

## What he should gain from this reading

He should gain this idea:

```text
test lets a script ask precise questions.
```

Examples:

```text
Is this a regular file?
Is this directory readable?
Is this string empty?
Are these two numbers equal?
```

This is where shell scripts become careful instead of hopeful.

---

# Before reading: Feynman preview

A script should not say:

```text
I hope the file is there.
```

A script should say:

```text
I checked whether the file is there before using it.
```

`test`, `[ ]`, and later `[[ ]]` are ways to ask those questions.

---

# After reading: concept questions

Answer without looking back:

1. What does the `test` command do?
2. How is `[ expression ]` related to `test expression`?
3. Why must there be spaces after `[` and before `]`?
4. What does `-f` test?
5. What does `-d` test?
6. What does `-r` test?
7. What does `-z` test?
8. What does `-n` test?
9. What is the difference between string comparison and integer comparison?
10. Why should variables usually be quoted inside `[ ]` tests?

Do not do the exercises until these are answered.

---

# Exercise 1: File tests

## Read before this exercise

Reread the file-expression part of:

```text
Using test
```

## Skill being gained

He is learning to test file facts before acting on files.

## Do not type yet: prediction table

Fill in:

```text
Path: sample.txt
-f expected:
-d expected:
-r expected:

Path: sample-dir
-f expected:
-d expected:
-r expected:
```

## Create test data

```bash
cd ~/tlcl-ch27-if

mkdir -p sample-dir
echo "hello" > sample.txt
```

## Create script

```bash
cat > file-tests.sh <<'EOF'
#!/usr/bin/env bash

path="$1"

if [ -f "$path" ]; then
    echo "$path is a regular file."
else
    echo "$path is not a regular file."
fi

if [ -d "$path" ]; then
    echo "$path is a directory."
else
    echo "$path is not a directory."
fi

if [ -r "$path" ]; then
    echo "$path is readable."
else
    echo "$path is not readable."
fi
EOF

bash file-tests.sh sample.txt
bash file-tests.sh sample-dir
bash file-tests.sh missing-path
```

## Read after this exercise

Reread the list of file expressions and choose five that look most useful for admin scripts.

## Explain-back

Answer:

1. Why did `sample.txt` pass `-f`?
2. Why did `sample-dir` fail `-f` but pass `-d`?
3. Why is `-r` useful before reading a file?
4. Why did we quote `$path`?
5. What could go wrong with `[ -f $path ]` if the path contains spaces?

---

# Exercise 2: String tests

## Read before this exercise

Reread the string-expression part of:

```text
Using test
```

## Skill being gained

He is learning to test whether text variables are empty or equal.

## Do not type yet: predict

Before running, predict for each value:

```text
name=""
-z result:
-n result:

name="server01"
-z result:
-n result:
```

## Create script

```bash
cat > string-tests.sh <<'EOF'
#!/usr/bin/env bash

name="$1"

if [ -z "$name" ]; then
    echo "No name was provided."
else
    echo "Name was provided: $name"
fi

if [ "$name" = "admin" ]; then
    echo "Name equals admin."
else
    echo "Name does not equal admin."
fi
EOF

bash string-tests.sh
bash string-tests.sh server01
bash string-tests.sh admin
```

## Read after this exercise

Reread the examples for `-z`, `-n`, `=`, and `!=`.

## Explain-back

Answer:

1. What does `-z` mean?
2. What does `-n` mean?
3. Why did the empty argument case choose “No name was provided”?
4. Why is `[ "$name" = "admin" ]` safer than `[ $name = admin ]`?
5. Why is this a string test, not an integer test?

---

# Exercise 3: Integer tests

## Read before this exercise

Reread the integer-expression part of:

```text
Using test
```

## Skill being gained

He is learning Bash's classic integer comparison operators.

## Important distinction

Inside `[ ]`, use these for integers:

```text
-eq  equal
-ne  not equal
-lt  less than
-le  less than or equal
-gt  greater than
-ge  greater than or equal
```

Do not use `<` and `>` for integer comparisons inside `[ ]`.

## Do not type yet: predict

For each age, predict the branch:

```text
age=12
adult branch or minor branch?

age=18
adult branch or minor branch?

age=20
adult branch or minor branch?
```

## Create script

```bash
cat > integer-tests.sh <<'EOF'
#!/usr/bin/env bash

age="$1"

if [ "$age" -ge 18 ]; then
    echo "Adult or adult-age student."
else
    echo "Minor-age student."
fi
EOF

bash integer-tests.sh 12
bash integer-tests.sh 18
bash integer-tests.sh 20
```

Now test a bad input:

```bash
bash integer-tests.sh apple
```

## Read after this exercise

Reread integer expressions and notice that classic integer tests expect integer-looking values.

## Explain-back

Answer:

1. What does `-ge` mean?
2. Why did `18` choose the adult branch?
3. What happened with `apple`?
4. Why does user input need validation before integer testing?
5. Which later chapter will make user input more important?

---

# Exercise 4: Add file tests to report script

## Read before this exercise

Reread file expressions in:

```text
Using test
```

## Skill being gained

He is learning to prevent a report script from assuming files are readable.

## Create script

```bash
cat > readable-file-report.sh <<'EOF'
#!/usr/bin/env bash

file="/etc/passwd"

echo "<h2>File Check</h2>"

if [ -r "$file" ]; then
    echo "<p>$file is readable.</p>"
    echo "<pre>"
    head -5 "$file"
    echo "</pre>"
else
    echo "<p>$file is not readable.</p>"
fi
EOF

bash readable-file-report.sh > readable-file-report.html
grep -A10 "File Check" readable-file-report.html
```

## Read after this exercise

Reread the file-test list and ask which tests would be useful before running backup, copy, or cleanup scripts.

## Explain-back

Answer:

1. What did the script check before using the file?
2. Why is this safer than just running `head -5 /etc/passwd`?
3. What would the failure branch do?
4. How could you test the failure branch safely?

---

# Day 2 finish standard

He is done only if he can explain:

```text
[ ] is a command form of test.
File tests ask about filesystem facts.
String tests ask about text.
Integer tests ask about whole numbers.
Quoting variables inside [ ] prevents many beginner bugs.
```
