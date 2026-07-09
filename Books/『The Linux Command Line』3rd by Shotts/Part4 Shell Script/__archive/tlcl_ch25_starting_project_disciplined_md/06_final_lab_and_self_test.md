# TLCL Chapter 25: Starting a Project

Use this after Chapter 24, “Writing Your First Script.”

Chapter 25 is not mainly about memorizing new syntax. It is about starting to think like a script writer:

```text
What output should this script produce?
What data belongs in the script?
What should be a variable?
What should stay constant?
How do I make the script readable enough to improve later?
```

Core discipline:

```text
Do not type a script line because the book shows it.
Type it only after you can explain what job that line does.
```

Feynman rule for this chapter:

```text
If you cannot explain the script to a younger student in plain English, you do not understand the script yet.
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch25-project/bin ~/tlcl-ch25-project/work ~/tlcl-ch25-project/versions
cd ~/tlcl-ch25-project
```

Start each session with:

```bash
pwd
ls -la
```

Then answer:

```text
Where am I?
What files already exist?
What will I create or modify today?
How will I test it?
```

---
# Session 6: Summing Up, Final Lab, and Self-Test

## Section to read before exercises

Read:

```text
Summing Up
```

Then skim the full chapter headings again:

```text
First Stage: Minimal Document
Second Stage: Adding a Little Data
Variables and Constants
Assigning Values to Variables and Constants
Here Documents
Summing Up
```

## What he should gain from this section

He should understand the chapter’s workflow:

```text
start small
add data
give repeated values names
assign dynamic command output
use here documents for clean multi-line output
```

## Feynman analogy

A script project is like growing a plant.

You do not tape fruit onto a stick and call it a tree.

You grow it in stages:

```text
seed = minimal script
stem = basic structure
leaves = useful data
roots = variables/constants
branches = command substitution
clean shape = here document
```

## Final lab: Create a small lab status page

The final script should produce an HTML-like report with:

```text
title
host name
current date
current user
current directory
number of files in current directory
```

## Step 1: Write design before code

Create:

```bash
cd ~/tlcl-ch25-project
cat > work/final-design.md <<'EOF'
# Final Script Design

Script name:

Purpose:

Output format:

Values that should be variables/constants:

Commands needed:

How I will test it:
EOF
```

Fill it in before writing code.

## Step 2: Read docs before using commands

Use:

```bash
man whoami
man pwd
man find
man wc
```

Or quick help:

```bash
whoami --help
pwd --help
find --help | head
wc --help | head
```

Answer:

```text
Which command prints current user?
Which command prints current directory?
Which command can count files with help from wc?
Where did I verify that?
```

## Step 3: Write the script

Create:

```bash
nano bin/lab_status_page
```

Suggested version:

```bash
#!/usr/bin/env bash

# Output a small lab status page

TITLE="Lab Status Page"
CURRENT_DATE=$(date +%F)
HOST_NAME=$(hostname)
CURRENT_USER=$(whoami)
CURRENT_DIR=$(pwd)
FILE_COUNT=$(find . -maxdepth 1 -type f | wc -l)

cat <<EOF
<html>
<head>
  <title>$TITLE</title>
</head>
<body>
  <h1>$TITLE</h1>
  <p>Date: $CURRENT_DATE</p>
  <p>Host: $HOST_NAME</p>
  <p>User: $CURRENT_USER</p>
  <p>Directory: $CURRENT_DIR</p>
  <p>Files in current directory: $FILE_COUNT</p>
</body>
</html>
EOF
```

## Step 4: Predict before running

Answer:

```text
Which commands run before cat?
Which values will change on another machine?
Which values will change in another directory?
What does find . -maxdepth 1 -type f | wc -l count?
Where does stdout go if I run normally?
```

## Step 5: Run and test

```bash
bash bin/lab_status_page
chmod 755 bin/lab_status_page
./bin/lab_status_page > work/lab-status.html
cat work/lab-status.html
```

Run from a different directory:

```bash
cd ~/tlcl-ch25-project/work
../bin/lab_status_page > lab-status-from-work.html
cat lab-status-from-work.html
```

## Step 6: Explain the bug or behavior

Question:

```text
Why did CURRENT_DIR and FILE_COUNT change when the script ran from a different directory?
```

Expected idea:

```text
pwd and find . depend on the current working directory at runtime.
```

This is not necessarily wrong. It is important to understand.

## Step 7: Explain every line

In `work/final-explanation.md`, explain:

```text
shebang:
comment:
TITLE assignment:
CURRENT_DATE assignment:
HOST_NAME assignment:
CURRENT_USER assignment:
CURRENT_DIR assignment:
FILE_COUNT assignment:
cat <<EOF:
variable expansion inside HTML:
ending EOF:
```

## Self-test: important concepts

Answer without notes:

1. Why should a project start with a minimal document?
2. What is the difference between assigning a value and expanding a value?
3. Why are spaces around `=` wrong in Bash assignment?
4. What does `$(hostname)` do?
5. What does `cat <<EOF` do?
6. Do variables expand inside an unquoted here document?
7. How do you prevent expansion inside a here document?
8. Why are variables useful in generated HTML?
9. Why should a script be tested after each small change?
10. Why does running the same script from a different directory sometimes change the result?

## Final standard

He understands Chapter 25 only if he can say:

```text
I can start with the smallest working script.
I can add output gradually.
I can use variables to avoid repeated values.
I can assign command output with $(...).
I can use a here document to generate multi-line output.
I can explain every line before improving the script.
```
