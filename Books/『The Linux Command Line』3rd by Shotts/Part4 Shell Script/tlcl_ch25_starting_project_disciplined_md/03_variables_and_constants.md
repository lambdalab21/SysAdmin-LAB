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

## After reading: concept questions

1. What is a variable? A variable is a named place to store a value. 
2. Why are names useful in scripts? Names are useful because they make scripts easier to read and reuse. 
3. What is the practical difference between typing a value many times and storing it once? Storing a value once is better because you only change it in one place instead of many places. 
4. Why might uppercase names be used for constant-like values? Uppercase names are used for constant-like values to show they should not change often. 
5. Why should variable names be meaningful? Variable names should be meaningful so that the script is easier to understand later. 

## Exercise 1: Identify repeated data

Look at the script:

```bash
cat bin/sys_report_page
```

Answer:

```text
Which text appears more than once? The repeated text is the title shown in both the <title> and <h1> tags. 
Which text might change later? The text that might change later is the report title. 
Which text deserves a name? The text deserves a name because it is used more than once. 
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
What exact text will $TITLE become? $TILE becomes System Information Report.
Will single quotes and double quotes behave the same here? Single Quotes and Double Quotes do not behave the same here. 
What would happen if I wrote '$TITLE' instead of "$TITLE"? If you wrote '$TITLE', it would print $TITLE literally instead of expanding the variable. 
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
How many output locations changed because one assignment changed? One assignment change updates both output locations, because this title is used in two places. 
```
