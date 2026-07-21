# Shotts Chapter 27: Flow Control — Branching with `if`

This guide is for Chapter 27 of William Shotts's *The Linux Command Line*: **Flow Control: Branching with if**.

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

---

# After reading: concept questions

Answer without looking back:

1. What does the `test` command do? The test command evaluates a condition and returns exit status 0 or 1. 
2. How is `[ expression ]` related to `test expression`? [expression] is a synonym for test expression. 
3. Why must there be spaces after `[` and before `]`? Spaces are required because [ is a command. Without them, shell parsing breaks. 
4. What does `-f` test? -i tests if a path is a regular file. 
5. What does `-d` test? -d tests if a path is a directory. 
6. What does `-r` test? -r tests if a file is readable by the current user. 
7. What does `-z` test? -z tests if a string is empty. 
8. What does `-n` test? -n tests if a string is not empty. 
9. What is the difference between string comparison and integer comparison? Strong comparison is lexica/textual. Integer comparison treats values as numbers. 
10. Why should variables usually be quoted inside `[ ]` tests? Quoting prevents word-splitting and globbing. Unquoted variables with spaces breaks the test.

---

# Exercise 1: File tests

## Read before this exercise

Reread the file-expression part of:

```text
Using test
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

1. Why did `sample.txt` pass `-f`? sample.txt passed -f because it is a regular file. 
2. Why did `sample-dir` fail `-f` but pass `-d`? sample-dir failed -f but passed -d. 
3. Why is `-r` useful before reading a file? -r is useful to avoid errors or permission-denied messages when trying to read a file. 
4. Why did we quote `$path`? Quoting protects against paths with spaces or special characters. 
5. What could go wrong with `[ -f $path ]` if the path contains spaces? Without quotes, spaces in the path cause [-f] to see multiple arguments and fail or give incorrect results

---

# Exercise 2: String tests

## Read before this exercise

Reread the string-expression part of:

```text
Using test
```

## Skill being gained

He is learning to test whether text variables are empty or equal.

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

1. What does `-z` mean? -z means that the string has zero length. 
2. What does `-n` mean? String has a non-zero length. 
3. Why did the empty argument case choose “No name was provided”? Empty argument made 
4. Why is `[ "$name" = "admin" ]` safer than `[ $name = admin ]`?
5. Why is this a string test, not an integer test?

---

# Exercise 3: Integer tests

## Read before this exercise

Reread the integer-expression part of:

```text
Using test
```

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

---

# Exercise 4: Add file tests to report script

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
