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
# Day 1: `if` Statements and Exit Status

## Read before exercises

Read these Chapter 27 sections before doing the exercises:

```text
if Statements
Exit Status
```

## What he should gain from this reading

He should gain this idea:

```text
Bash does not branch on vague human opinions.
Bash branches on command exit status.
```

Important rule:

```text
exit status 0       = success / true enough for if
exit status nonzero = failure / false enough for if
```

This may feel backwards because in many languages `0` feels false. In shell scripting, `0` means the command succeeded.

---

# Before reading: Feynman preview

Imagine asking a helper:

```text
Did this command succeed?
```

The helper answers only with a number:

```text
0 = yes
1 or other nonzero number = no
```

An `if` statement listens to that answer.

---

# After reading: concept questions

Answer without looking back:

1. What is an exit status?
2. What exit status means success?
3. What exit status means failure?
4. Why does `if grep pattern file; then ...` make sense?
5. Why must `$?` be checked immediately after the command of interest?
6. What is the basic shape of an `if` statement?
7. What are `then`, `else`, and `fi` doing?

Do not do the exercises until these are answered.

---

# Exercise 1: See exit status directly

## Read before this exercise

Reread:

```text
Exit Status
```

## Skill being gained

He is learning that commands report success or failure numerically.

## Do not type yet: predict

Fill in:

```text
Command: true
Expected exit status:
Reason:

Command: false
Expected exit status:
Reason:

Command: ls existing-file
Expected exit status:
Reason:

Command: ls missing-file
Expected exit status:
Reason:
```

## Run

```bash
cd ~/tlcl-ch27-if

touch existing-file

true
echo "$?"

false
echo "$?"

ls existing-file >/dev/null
echo "$?"

ls missing-file >/dev/null 2>&1
echo "$?"
```

## Read after this exercise

Reread the short part of `Exit Status` that explains how `if` uses command success or failure.

## Explain-back

Answer:

1. Why did `true` return `0`?
2. Why did `false` return nonzero?
3. Why did `ls missing-file` fail?
4. What does `>/dev/null` hide?
5. What does `2>&1` or `2>/dev/null` hide?
6. Why is hiding output sometimes useful when the real question is success/failure?

---

# Exercise 2: First `if` statement

## Read before this exercise

Reread:

```text
if Statements
```

Pay attention to the structure:

```bash
if commands; then
    commands
else
    commands
fi
```

## Skill being gained

He is learning that `if` can run any command as a test.

## Do not type yet: prediction table

Fill this in before running:

```text
Test command:
Expected branch if file exists:
Expected branch if file is missing:
How I will test both cases:
```

## Create script

```bash
cat > check-file-v1.sh <<'EOF'
#!/usr/bin/env bash

file="existing-file"

if ls "$file" >/dev/null 2>&1; then
    echo "$file exists."
else
    echo "$file does not exist."
fi
EOF

bash check-file-v1.sh
```

Now edit the script and change:

```bash
file="existing-file"
```

to:

```bash
file="missing-file"
```

Run again:

```bash
bash check-file-v1.sh
```

## Read after this exercise

Reread the `if Statements` section and notice that `if` tests the exit status of commands, not just special bracket syntax.

## Explain-back

Answer:

1. What command did `if` actually run?
2. Why did the script choose the first branch when the file existed?
3. Why did it choose the second branch when the file was missing?
4. Why is this version not yet the best way to test files?
5. What tool does the next section introduce for cleaner tests?

---

# Exercise 3: Branching inside the report project

## Read before this exercise

Reread:

```text
if Statements
Exit Status
```

## Skill being gained

He is learning to add one branch to a real script without breaking the whole project.

## Create a small report script

```bash
cat > mini-report.sh <<'EOF'
#!/usr/bin/env bash

report_header () {
    echo "<html>"
    echo "<body>"
    echo "<h1>Mini Report</h1>"
}

report_hostname () {
    echo "<h2>Hostname</h2>"
    if hostname >/dev/null 2>&1; then
        echo "<p>hostname command is available.</p>"
        echo "<pre>"
        hostname
        echo "</pre>"
    else
        echo "<p>hostname command failed.</p>"
    fi
}

report_footer () {
    echo "</body>"
    echo "</html>"
}

report_header
report_hostname
report_footer
EOF

bash mini-report.sh > mini-report.html
grep -A6 "Hostname" mini-report.html
```

## Read after this exercise

Reread the beginning of Chapter 27 where Shotts explains adapting the report generator based on conditions.

## Explain-back

Answer:

1. Why did this report branch?
2. What condition did it test?
3. What output proves the success branch ran?
4. How would you force or simulate the failure branch?
5. Why is this more useful than a report that blindly assumes every command works?

---

# Day 1 finish standard

He is done only if he can explain:

```text
An if statement tests the exit status of a command.
0 means success.
Nonzero means failure.
The script should test both branches before I trust it.
```
