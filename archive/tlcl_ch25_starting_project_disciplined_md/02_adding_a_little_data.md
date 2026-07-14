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
# Session 2: Second Stage — Adding a Little Data

## Section to read before exercises

Read only:

```text
Second Stage: Adding a Little Data
```

## What he should gain from this section

He should learn:

```text
Add one small piece of useful output, then test again.
```

A script should grow in controlled steps.

## Feynman analogy

A beginner dumps a pile of parts on the table.

A disciplined builder adds one part, checks whether the machine still works, then adds the next part.

In scripting:

```text
small change
→ run
→ inspect
→ explain
→ commit mentally
→ next change
```

## After reading: concept questions

1. What kind of data is the script beginning to add?
2. Why should output structure matter?
3. Why is it better to add a little data before adding logic?
4. What would make the script harder to debug?
5. How will he know whether the script still works?

## Exercise 1: Copy the previous version

```bash
cd ~/tlcl-ch25-project
cp bin/sys_report_page versions/sys_report_page_session1
```

Question:

```text
Why save a previous working version?
```

Expected idea:

```text
If the new version breaks, I can compare or restore.
```

## Exercise 2: Add a title and body

Edit:

```bash
nano bin/sys_report_page
```

Change it to:

```bash
#!/usr/bin/env bash

# Program to output a system information page

echo "<html>"
echo "<head>"
echo "  <title>System Information Report</title>"
echo "</head>"
echo "<body>"
echo "  <h1>System Information Report</h1>"
echo "</body>"
echo "</html>"
```

## Predict before running

Answer:

```text
How many HTML tags will appear?
Which lines are structure?
Which line is visible page content?
Where will stdout go if I run the script normally?
```

## Run and inspect

```bash
bash bin/sys_report_page
./bin/sys_report_page > work/report.html
cat work/report.html
```

Check line count:

```bash
wc -l work/report.html
```

## Exercise 3: Break it deliberately, then fix it

Make a temporary mistake: remove one closing quote from an `echo` line.

Run:

```bash
bash bin/sys_report_page
```

Then restore the quote and run again.

## Explain what happened

Answer:

```text
What error did Bash show?
Was the problem in HTML or shell syntax?
How did I know where to look?
```

## Disciplined-thinking checkpoint

Before moving on, answer:

1. Why do we add data gradually?
2. What did the script output before?
3. What does it output now?
4. What changed?
5. What stayed the same?
