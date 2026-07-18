 #Book/The-Linux-Command-Line 
#shell-script #bash-script 
# TLCL Chapter 25: Starting a Project

Use this after Chapter 24, “Writing Your First Script.”

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
Which command prints current user? whoami is the command that prints the current user
Which command prints current directory? the command that prints the current directory is pwd. 
Which command can count files with help from wc? find . -maxdepth 1 -type f | wc -l. 
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
Which commands run before cat? date, hostname, whoami, pwd, and find run before cat because their results are stored in variables first. 
Which values will change on another machine? HOST_NAME and CURRENT_USER can change on another machine. 
Which values will change in another directory? CURRENT_DIR and FILE_COUNT can change in another directory. 
What does find . -maxdepth 1 -type f | wc -l count? find . -maxdepth 1 -type f | wc -1 counts regular files in the current directory. 
Where does stdout go if I run normally? If you run it normally, stdout goes to the terminal unless you redirect it.
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
Why did CURRENT_DIR and FILE_COUNT change when the script ran from a different directory? CURRENT_DIR and FILE_COUNT changed because pwd and find . depending on the working directory when the script runs. 
```

Expected idea:

```text
pwd and find . depend on the current working directory at runtime.
```

This is not necessarily wrong. It is important to understand.


## Self-test: important concepts

Answer without notes:

1. Why should a project start with a minimal document? A project should start with a minimal document so that it is easy to understand and build step by step. 
2. What is the difference between assigning a value and expanding a value? Assigning a value stores it in a variable; where expanding a value reads with with $. 
3. Why are spaces around `=` wrong in Bash assignment? Spaces around = are wrong because bash does not treat that as assignment. 
4. What does `$(hostname)` do? $(hostname) runs hostname and stores its output. 
5. What does `cat <<EOF` do? It starts a here document and sends the following lines to cat until EOF. 
6. Do variables expand inside an unquoted here document? Yes, variables expand inside an unquoted here document. 
7. How do you prevent expansion inside a here document? Use a quoted delimiter like <<'EOF' to prevent expansion. 
8. Why are variables useful in generated HTML? Variables are useful in generated HTML because they avoid repeating values and make updates easier. 
9. Why should a script be tested after each small change? A script should be tested after each small change so that mistakes are easier to find. 
10. Why does running the same script from a different directory sometimes change the result? Running the same script from a different directory can change the result because some commands use the current location ate runtime. 
