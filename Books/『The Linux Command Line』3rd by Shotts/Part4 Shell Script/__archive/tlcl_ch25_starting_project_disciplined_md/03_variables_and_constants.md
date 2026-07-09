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
# Session 3: Variables and Constants

## Section to read before exercises

Read only:

```text
Variables and Constants
```

## What he should gain from this section

He should learn:

```text
Names can hold values, and good names make scripts easier to change.
```

He should also begin to distinguish:

```text
constant-like values = values the script sets and usually does not change
variable values      = values that may change during execution or between runs
```

Shell does not enforce constants the way some languages do. The discipline is mostly human discipline.

## Feynman analogy

Imagine writing a school report.

Bad version:

```text
The same title is typed by hand in ten places.
```

Good version:

```text
Write the title once on an index card.
Whenever the report needs the title, copy it from the card.
```

A shell variable is that index card.

## After reading: concept questions

1. What is a variable?
2. Why are names useful in scripts?
3. What is the practical difference between typing a value many times and storing it once?
4. Why might uppercase names be used for constant-like values?
5. Why should variable names be meaningful?

## Exercise 1: Identify repeated data

Look at the script:

```bash
cat bin/sys_report_page
```

Answer:

```text
Which text appears more than once?
Which text might change later?
Which text deserves a name?
```

## Exercise 2: Add title variable

Edit:

```bash
nano bin/sys_report_page
```

Use:

```bash
#!/usr/bin/env bash

# Program to output a system information page

TITLE="System Information Report"

echo "<html>"
echo "<head>"
echo "  <title>$TITLE</title>"
echo "</head>"
echo "<body>"
echo "  <h1>$TITLE</h1>"
echo "</body>"
echo "</html>"
```

## Predict before running

Answer:

```text
What exact text will $TITLE become?
Will single quotes and double quotes behave the same here?
What would happen if I wrote '$TITLE' instead of "$TITLE"?
```

## Run and inspect

```bash
bash bin/sys_report_page > work/report.html
cat work/report.html
```

## Exercise 3: Test expansion deliberately

Run:

```bash
TITLE="Test Title"
echo "$TITLE"
echo '$TITLE'
```

Explain:

```text
Double quotes allow variable expansion.
Single quotes prevent variable expansion.
```

## Exercise 4: Change one value

Change:

```bash
TITLE="System Information Report"
```

to:

```bash
TITLE="Lab Machine Report"
```

Run again:

```bash
bash bin/sys_report_page > work/report.html
cat work/report.html
```

Question:

```text
How many output locations changed because one assignment changed?
```

## Disciplined-thinking checkpoint

He is done only if he can answer:

1. Why was `TITLE` useful?
2. What does `$TITLE` mean?
3. Why did double quotes matter?
4. What would single quotes do?
5. How did the script become easier to change?
