# Shotts Chapter 26: Top-Down Design

This guide is for Chapter 26, **Top-Down Design**, from William Shotts's *The Linux Command Line*.

The goal is not merely to type functions.

The goal is to learn this habit:

```text
Start with the big job.
Break it into named smaller jobs.
Make each piece work.
Test after every small change.
Keep the script runnable.
```

Feynman analogy:

```text
A messy script is like a long paragraph with no headings.
Functions are like section headings.
They let the reader see the shape of the work before reading every detail.
```

Main project for all three days:

```text
lab-report.sh
```

It will grow from a simple report script into a script organized with functions.

Create the working directory once:

```bash
mkdir -p ~/tlcl-ch26-top-down
cd ~/tlcl-ch26-top-down
```

Disciplined thinking rule:

```text
Before typing a function, say what job it owns.
Before running a script, predict what section should print.
After running, inspect the output and explain what changed.
```

---
# Day 1: Top-Down Design and Shell Functions

## Read before exercises

Read Chapter 26 from the beginning through the section:

```text
Shell Functions
```

## What he should gain from this reading

He should gain this idea:

```text
A script can be designed from the top down.
The main body should show the big structure.
Functions fill in the details.
```

He is learning **organization**, not just syntax.

---

# Before reading: Feynman preview

Explain this to yourself before reading:

```text
If I had to write a report by hand, I would first make headings:
Title
System information
Disk space
Home directory usage

Only after that would I fill in each section.
```

That is top-down design.

In a shell script:

```text
main body = report outline
functions = section writers
```

---

# After reading: concept questions

Answer without looking back:

1. What is top-down design?
2. Why is it better than writing a long script from top to bottom?
3. What is a shell function?
4. Why can a function make a script easier to read?
5. Where should the function definition appear before it is used?
6. What does it mean to “stub out” a function?
7. Why should the script still run even when not all parts are finished?

Do not do the exercises until these are answered.

---

# Exercise 1: Start with a visible skeleton

## Skill being gained

He is learning to make the script structure visible before filling in details.

## Predict before typing

Before creating the file, answer:

```text
What sections should my report eventually have?
Which parts can be placeholders today?
What output do I expect when I run the first version?
```

## Create the script

```bash
cd ~/tlcl-ch26-top-down

cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

# lab-report.sh - simple lab report built with top-down design

echo "<html>"
echo "<head><title>Lab Report</title></head>"
echo "<body>"
echo "<h1>Lab Report</h1>"

echo "<h2>System Information</h2>"
echo "<p>TODO: system information goes here.</p>"

echo "<h2>Disk Space</h2>"
echo "<p>TODO: disk space report goes here.</p>"

echo "<h2>Home Directory</h2>"
echo "<p>TODO: home directory report goes here.</p>"

echo "</body>"
echo "</html>"
EOF
```

Run safely:

```bash
bash lab-report.sh > report.html
head report.html
```

Open or inspect:

```bash
less report.html
```

## Explain-back

Explain:

```text
Why did we redirect output to report.html?
Why did we run with bash first instead of chmod +x first?
What parts are placeholders?
What is the script's current structure?
```

---

# Exercise 2: Convert sections into functions

## Skill being gained

He is learning that functions are named pieces of work.

## Read again before exercise

Reread the syntax for shell functions in the “Shell Functions” section.

Pay attention to the form:

```bash
function_name () {
    commands
}
```

or equivalent function syntax if the book shows it.

## Predict before typing

Answer:

```text
What function names should I create?
What should each function print?
What should the main body look like after functions exist?
```

## Rewrite the script

```bash
cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

# lab-report.sh - simple lab report built with top-down design

report_header () {
    echo "<html>"
    echo "<head><title>Lab Report</title></head>"
    echo "<body>"
    echo "<h1>Lab Report</h1>"
}

report_system_info () {
    echo "<h2>System Information</h2>"
    echo "<p>TODO: system information goes here.</p>"
}

report_disk_space () {
    echo "<h2>Disk Space</h2>"
    echo "<p>TODO: disk space report goes here.</p>"
}

report_home_space () {
    echo "<h2>Home Directory</h2>"
    echo "<p>TODO: home directory report goes here.</p>"
}

report_footer () {
    echo "</body>"
    echo "</html>"
}

report_header
report_system_info
report_disk_space
report_home_space
report_footer
EOF
```

Run:

```bash
bash lab-report.sh > report.html
less report.html
```

## Explain-back

For each function, fill in:

```text
Function name:
Job:
Input:
Output:
Does it change files?
```

Example:

```text
Function name: report_header
Job: print the beginning of the HTML page
Input: none
Output: HTML text to stdout
Does it change files? no
```

---

# Exercise 3: Replace one placeholder with real output

## Skill being gained

He is learning stepwise refinement: replace one placeholder at a time.

## Predict before typing

Answer:

```text
Which function will I improve?
What command will provide the data?
How will I keep the script runnable if my idea is wrong?
```

## Improve system info

Edit only this function:

```bash
report_system_info () {
    echo "<h2>System Information</h2>"
    echo "<pre>"
    hostname
    date
    uname -a
    echo "</pre>"
}
```

Use an editor or rewrite the file carefully.

Then test:

```bash
bash lab-report.sh > report.html
grep -A5 "System Information" report.html
```

## Explain-back

Answer:

1. What changed?
2. Which function did you edit?
3. Did other functions need to change?
4. Why is that good design?
5. What would be harder if this were one long script?

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
Top-down design means I start with the big structure and refine the pieces.
A function is a named block of commands.
The main body of the script should read like an outline.
I can replace one placeholder at a time while keeping the script runnable.
```

He must also be able to explain every line of `lab-report.sh`.
