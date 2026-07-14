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
# Day 4: Combining Expressions, Control Operators, and Final Lab

## Read before exercises

Read these Chapter 27 sections:

```text
Combining Expressions
Control Operators: Another Way to Branch
Summing Up
```

## What he should gain from this reading

He should gain this idea:

```text
Real decisions often require more than one condition.
But clever one-line branching can become hard to read.
```

Disciplined rule:

```text
Use if when clarity matters.
Use && and || for short, obvious cases.
Do not write clever command chains you cannot explain.
```

---

# Before reading: Feynman preview

A real decision might be:

```text
Run the command only if the file exists AND is readable.
Warn the user if the file is missing OR empty.
```

This is no longer a single fact. It is a combined condition.

---

# After reading: concept questions

Answer without looking back:

1. What does logical AND mean?
2. What does logical OR mean?
3. How can expressions be combined inside `[[ ]]`?
4. How can arithmetic expressions be combined inside `(( ))`?
5. What does `command1 && command2` mean?
6. What does `command1 || command2` mean?
7. Why can `&&` and `||` be useful but also dangerous for readability?
8. When is a full `if` statement better than a command chain?

Do not do the exercises until these are answered.

---

# Exercise 1: Combine file conditions

## Read before this exercise

Reread:

```text
Combining Expressions
```

## Skill being gained

He is learning to require multiple facts before acting.

## Do not type yet: predict

For `sample.txt`, predict:

```text
Is it a regular file?
Is it readable?
Should the success branch run?
```

For `sample-dir`, predict:

```text
Is it a regular file?
Is it readable?
Should the success branch run?
```

## Create script

```bash
cd ~/tlcl-ch27-if

mkdir -p sample-dir
echo "hello" > sample.txt

cat > combined-file-test.sh <<'EOF'
#!/usr/bin/env bash

path="$1"

if [[ -f $path && -r $path ]]; then
    echo "$path is a readable regular file."
else
    echo "$path is not both readable and a regular file."
fi
EOF

bash combined-file-test.sh sample.txt
bash combined-file-test.sh sample-dir
bash combined-file-test.sh missing-path
```

## Read after this exercise

Reread the part of `Combining Expressions` about AND.

## Explain-back

Answer:

1. What two facts had to be true?
2. Why did `sample-dir` fail the combined test?
3. Why is `&&` inside `[[ ]]` different from using `&&` between commands?
4. How would you rewrite this as nested `if` statements?

---

# Exercise 2: Combine arithmetic conditions

## Read before this exercise

Reread:

```text
Combining Expressions
(( ))—Designed for Integers
```

## Skill being gained

He is learning to combine numeric decisions clearly.

## Do not type yet: predict

A valid training load is between 1 and 100.

Predict:

```text
load=0:
load=50:
load=100:
load=101:
```

## Create script

```bash
cat > numeric-range-test.sh <<'EOF'
#!/usr/bin/env bash

load="$1"

if (( load >= 1 && load <= 100 )); then
    echo "load is in the expected range."
else
    echo "load is outside the expected range."
fi
EOF

bash numeric-range-test.sh 0
bash numeric-range-test.sh 50
bash numeric-range-test.sh 100
bash numeric-range-test.sh 101
```

## Read after this exercise

Reread the arithmetic and combining-expression sections.

## Explain-back

Answer:

1. Why did `50` pass?
2. Why did `0` fail?
3. Why did `101` fail?
4. Why was `(( ))` the right form here?
5. Why would this still need validation if the number came from a user?

---

# Exercise 3: Control operators as short branches

## Read before this exercise

Reread:

```text
Control Operators: Another Way to Branch
```

## Skill being gained

He is learning what `&&` and `||` mean between commands.

## Do not type yet: predict

Predict each result:

```text
true && echo success:
false && echo success:
true || echo failure:
false || echo failure:
```

## Run

```bash
true && echo "success after true"
false && echo "success after false"
true || echo "failure after true"
false || echo "failure after false"
```

Now use real commands:

```bash
[[ -f sample.txt ]] && echo "sample.txt exists"
[[ -f missing-path ]] || echo "missing-path is missing"
```

## Read after this exercise

Reread the control-operator section and ask whether each example would be clearer as a full `if` statement.

## Explain-back

Answer:

1. What does `&&` mean between commands?
2. What does `||` mean between commands?
3. Why did `false && echo ...` not print?
4. Why did `false || echo ...` print?
5. When should you avoid command chains and use full `if` instead?

---

# Exercise 4: Final lab — branching `lab-report.sh`

## Read before this exercise

Reread all of these Chapter 27 sections:

```text
if Statements
Exit Status
Using test
A More Modern Version of test
(( ))—Designed for Integers
Combining Expressions
Control Operators: Another Way to Branch
Summing Up
```

## Skill being gained

He is learning to use branching in a realistic report script.

This final script uses:

```text
functions from Chapter 26
file/string/integer decisions from Chapter 27
exit-status checks
modern [[ ]]
arithmetic (( ))
```

## Do not type yet: design table

Before creating the script, fill in:

```text
Function: report_user
Condition tested:
Success branch:
Failure branch:

Function: report_passwd_preview
Condition tested:
Success branch:
Failure branch:

Function: report_disk_space
Condition tested:
Success branch:
Failure branch:

Function: report_home_space
Condition tested:
Success branch:
Failure branch:
```

## Create final script

```bash
cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

# lab-report.sh - HTML report with Chapter 27 branching

report_header () {
    echo "<html>"
    echo "<head><title>Lab Report</title></head>"
    echo "<body>"
    echo "<h1>Lab Report</h1>"
}

report_user () {
    local title="User Privilege"
    echo "<h2>$title</h2>"

    if (( EUID == 0 )); then
        echo "<p>This script is running as root.</p>"
    else
        echo "<p>This script is not running as root.</p>"
    fi
}

report_system_info () {
    local title="System Information"
    echo "<h2>$title</h2>"

    if command -v hostname >/dev/null 2>&1; then
        echo "<pre>"
        hostname
        date
        uname -a
        echo "</pre>"
    else
        echo "<p>hostname command is not available.</p>"
    fi
}

report_passwd_preview () {
    local title="Readable File Check"
    local file="/etc/passwd"
    echo "<h2>$title</h2>"

    if [[ -f $file && -r $file ]]; then
        echo "<p>$file is a readable regular file.</p>"
        echo "<pre>"
        head -5 "$file"
        echo "</pre>"
    else
        echo "<p>$file is not readable or is not a regular file.</p>"
    fi
}

report_disk_space () {
    local title="Disk Space"
    echo "<h2>$title</h2>"

    if df -h >/dev/null 2>&1; then
        echo "<pre>"
        df -h
        echo "</pre>"
    else
        echo "<p>Could not collect disk-space information.</p>"
    fi
}

report_home_space () {
    local title="Home Directory"
    echo "<h2>$title</h2>"

    if [[ -n $HOME && -d $HOME ]]; then
        echo "<pre>"
        du -sh "$HOME" 2>/dev/null
        echo "</pre>"
    else
        echo "<p>Home directory is not available.</p>"
    fi
}

report_footer () {
    echo "</body>"
    echo "</html>"
}

report_header
report_user
report_system_info
report_passwd_preview
report_disk_space
report_home_space
report_footer
EOF
```

## Test syntax

```bash
bash -n lab-report.sh
```

## Run and inspect

```bash
bash lab-report.sh > report.html

grep -E "<h1>|<h2>|root|readable|not readable|not running" report.html
```

## Make executable only after testing

```bash
chmod +x lab-report.sh
./lab-report.sh > report.html
```

## Read after this exercise

Reread:

```text
Summing Up
```

Then reread whichever section explains the part that felt weakest:

```text
if Statements
Exit Status
Using test
A More Modern Version of test
(( ))—Designed for Integers
Combining Expressions
Control Operators
```

## Explain-back

Answer:

1. Which function used arithmetic evaluation?
2. Which function used file tests?
3. Which function used command exit status?
4. Which function combined expressions?
5. Why is `[[ -n $HOME && -d $HOME ]]` more careful than just running `du -sh "$HOME"`?
6. Why is `command -v hostname` a better check than assuming `hostname` exists?
7. Which branches are hard to test on your machine?
8. How could you safely simulate a failure branch?

---

# Exercise 5: Rewrite one command-chain as full `if`

## Read before this exercise

Reread:

```text
Control Operators: Another Way to Branch
```

## Skill being gained

He is learning that shorter is not always clearer.

## Starting command chain

```bash
[[ -f sample.txt ]] && echo "exists" || echo "missing"
```

## Rewrite as full `if`

```bash
if [[ -f sample.txt ]]; then
    echo "exists"
else
    echo "missing"
fi
```

## Explain-back

Answer:

1. Which version is easier to read?
2. Which version is easier to expand later?
3. When is the one-line version acceptable?
4. When is the full `if` version better?

---

# Final Chapter 27 self-test

Answer in writing:

1. What does `if` actually test?
2. What does exit status `0` mean?
3. What does nonzero exit status mean?
4. What is the difference between `[ ]` and `[[ ]]`?
5. What is `(( ))` for?
6. What is the difference between string comparison and integer comparison?
7. Why is `[[ $host == app* ]]` not regex?
8. Why can `&&` mean different-looking things depending on whether it is inside `[[ ]]` or between commands?
9. When should a script use a full `if` statement instead of `cmd && cmd`?
10. How did Chapter 27 make the report script smarter?

---

# Final Feynman explanation

Explain Chapter 27 to a younger student:

```text
A shell script can choose what to do.
It chooses by running a command or test and checking whether it succeeded.
Success is exit status 0.
Failure is nonzero.
For files and strings, Bash often uses [[ ]].
For arithmetic, Bash often uses (( )).
For simple cases, && and || can act like tiny branches.
For clear scripts, full if statements are often better.
```

If he cannot explain that without notes, he should review before moving to Chapter 28.

---

# Day 4 finish standard

He is done with Chapter 27 only if he can write from scratch:

```bash
if [[ -f $file && -r $file ]]; then
    echo "readable file"
else
    echo "not usable"
fi
```

and explain every symbol:

```text
if
[[
-f
$file
&&
-r
then
else
fi
```
