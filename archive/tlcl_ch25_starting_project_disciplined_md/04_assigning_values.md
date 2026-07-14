 #Book/The-Linux-Command-Line 
#shell-script #bash-script 
 #Book/The-Linux-Command-Line 
#shell-script #bash-script 
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
# Session 4: Assigning Values to Variables and Constants

## Section to read before exercises

Read only:

```text
Assigning Values to Variables and Constants
```

## What he should gain from this section

He should learn that assignment syntax is exact:

```bash
NAME=value
```

No spaces around `=`.

He should also learn that command substitution can put command output into a variable.

## Feynman analogy

A variable assignment is like labeling a box.

```bash
TITLE="System Information Report"
```

means:

```text
Put this text into the box named TITLE.
```

But Bash is strict. This is not the same:

```bash
TITLE = "System Information Report"
```

That looks like English, but Bash does not read it that way.

## After reading: concept questions

1. Why are spaces around `=` wrong in shell variable assignment?
2. What is the difference between `NAME=value` and `$NAME`?
3. When do you use the variable name without `$`?
4. When do you use the variable name with `$`?
5. What is command substitution useful for?

## Exercise 1: Test assignment syntax

Run:

```bash
TITLE="Correct Assignment"
echo "$TITLE"
```

Now deliberately run:

```bash
TITLE = "Wrong Assignment"
```

Then:

```bash
echo "$TITLE"
```

Explain:

```text
What did Bash try to do with TITLE = "Wrong Assignment"?
Why is that not assignment?
```

## Exercise 2: Add dynamic values

Edit `bin/sys_report_page`:

```bash
nano bin/sys_report_page
```

Use:

```bash
#!/usr/bin/env bash

# Program to output a system information page

TITLE="System Information Report"
CURRENT_TIME=$(date)
HOST_NAME=$(hostname)

echo "<html>"
echo "<head>"
echo "  <title>$TITLE</title>"
echo "</head>"
echo "<body>"
echo "  <h1>$TITLE</h1>"
echo "  <p>Generated: $CURRENT_TIME</p>"
echo "  <p>Host: $HOST_NAME</p>"
echo "</body>"
echo "</html>"
```

## Predict before running

Answer:

```text
When is date executed?
When is hostname executed?
What will CURRENT_TIME contain?
What will HOST_NAME contain?
Will the values change if I run the script again later?
```

## Run and inspect

```bash
bash bin/sys_report_page > work/report.html
cat work/report.html
```

Run again after a few seconds:

```bash
sleep 3
bash bin/sys_report_page > work/report2.html
diff work/report.html work/report2.html || true
```

## Explain the difference

Answer:

```text
Which line changed?
Why did it change?
Which value stayed the same?
Why did it stay the same?
```

## Exercise 3: Read documentation habit

Use documentation before adding any new command substitution:

```bash
date --help | head
man date
hostname --help 2>/dev/null | head || man hostname
```

Answer:

```text
Which option would let date print only year-month-day?
Where did I find that information?
```

Try:

```bash
date +%F
```

Then update:

```bash
CURRENT_DATE=$(date +%F)
```

Add a line to the report if desired.

## Disciplined-thinking checkpoint

He is done only if he can answer:

1. Why must assignment have no spaces around `=`?
2. What does `$(date)` do?
3. Why is `$(...)` better than manually typing the date?
4. What changed between two runs?
5. Did he use `--help` or `man` before adding an option?
