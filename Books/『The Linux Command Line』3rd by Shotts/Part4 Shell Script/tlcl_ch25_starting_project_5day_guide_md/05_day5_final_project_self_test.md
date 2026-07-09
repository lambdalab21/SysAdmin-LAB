# Shotts Chapter 25: Starting a Project

This guide is for Chapter 25, **Starting a Project**, from William Shotts's *The Linux Command Line*.

The goal is not to type an HTML-report script mindlessly. The goal is to learn how a script grows from a tiny working version into a useful project.

Core habit:

```text
small working version
→ inspect output
→ explain every line
→ improve one small part
→ test again
```

Feynman analogy:

```text
A script project is like building a small model house.
You do not begin with plumbing, paint, furniture, and wiring all at once.
You first build a simple frame that stands.
Then you add one feature at a time.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch25-project
cd ~/tlcl-ch25-project
```

Daily discipline:

```text
Before typing:
  What am I trying to make the script do?
  What output do I expect?
  Which line is responsible for which part?

After running:
  Did it run?
  Did it produce the expected output?
  Can I explain every line?
  What is the next smallest improvement?
```

---
# Day 5: Final Project and Self-Test

## Section to read before exercises

Read Chapter 25 section:

```text
Summing Up
```

Then skim the whole chapter again, especially:

```text
First Stage: Minimal Document
Second Stage: Adding a Little Data
Variables and Constants
Assigning Values to Variables and Constants
Here Documents
```

## What he should be gaining

He should now understand the chapter as a project-development method:

```text
Start small.
Add data gradually.
Use names for important values.
Use here documents for readable blocks.
Test constantly.
```

---

# Before exercise: Feynman summary

Explain Chapter 25 in five sentences:

```text
1. A script project should begin with the smallest working version.
2. A report script can print HTML to stdout.
3. Redirecting stdout creates the report file.
4. Variables make repeated values easier to change.
5. Here documents make blocks of output easier to read.
```

If he cannot explain these, he should review the earlier days.

---

# Final Project: Clean `sys_info_page.sh`

## Skill being trained

He is learning to produce a clean small project and explain its design.

## Predict before typing

Answer:

```text
What sections will the HTML report have?
What variable will control the title?
Which parts use here documents?
Which parts use command output?
Where will the final HTML go?
```

## Create the final version

```bash
cd ~/tlcl-ch25-project

cat > sys_info_page.sh <<'EOF'
#!/usr/bin/env bash

# sys_info_page.sh - generate a simple system information page

TITLE="System Information Report"

cat <<HTML
<html>
<head>
<title>$TITLE</title>
</head>
<body>
<h1>$TITLE</h1>
HTML

cat <<HTML
<h2>System Identity</h2>
<pre>
HTML

hostname
date
uname -a

cat <<HTML
</pre>
<h2>Disk Space</h2>
<pre>
HTML

df -h

cat <<HTML
</pre>
</body>
</html>
HTML
EOF
```

Check syntax:

```bash
bash -n sys_info_page.sh
```

Run:

```bash
bash sys_info_page.sh > sys_info_page.html
```

Inspect:

```bash
head sys_info_page.html
grep -E "<h1>|<h2>" sys_info_page.html
tail sys_info_page.html
```

Make executable:

```bash
chmod +x sys_info_page.sh
./sys_info_page.sh > sys_info_page.html
```

---

# Explain every line

Fill this out:

```text
Line or block:
Purpose:
Output produced:
Could it fail?
How would I test it?
```

Required blocks:

```text
shebang
TITLE assignment
first here document
hostname/date/uname block
df -h block
final here document
redirection to sys_info_page.html
chmod +x
./sys_info_page.sh
```

---

# Self-test 1: Concepts

Answer without notes:

1. Why start with a minimal document?
2. What does stdout mean?
3. Why can a script generate a file without directly opening a file itself?
4. What does `>` do when running the script?
5. What is a variable?
6. Why must assignment have no spaces around `=`?
7. Why quote variable expansions?
8. What is a here document?
9. What does the here-document delimiter do?
10. What is the difference between quoted and unquoted delimiters?

---

# Self-test 2: Diagnose mistakes

Explain what is wrong.

## Mistake 1

```bash
TITLE = "System Information Report"
```

Expected issue:

```text
Shell assignment cannot have spaces around =.
```

## Mistake 2

```bash
cat <<HTML
<html>
</html>
HTM
```

Expected issue:

```text
The ending delimiter does not match the opening delimiter.
```

## Mistake 3

```bash
bash sys_info_page.sh
```

and the user says:

```text
I expected a file to appear.
```

Expected issue:

```text
Without redirection, output goes to the terminal. Use > sys_info_page.html.
```

## Mistake 4

```bash
./sys_info_page.sh
```

returns:

```text
Permission denied
```

Expected issue:

```text
The script may not be executable. Use chmod +x sys_info_page.sh or run bash sys_info_page.sh.
```

---

# Self-test 3: Modify carefully

Add a new section:

```text
Logged-in user
```

Rules:

```text
Do not rewrite the whole script.
Add the smallest useful section.
Run bash -n.
Run the script.
Inspect only the new section.
Explain the change.
```

Possible command:

```bash
whoami
```

Possible inspection:

```bash
grep -A5 "Logged-in" sys_info_page.html
```

---

# Final standard

He understands Chapter 25 only if he can say:

```text
I can start a script project with a minimal working version.
I can generate structured output through stdout.
I can redirect output to a file.
I can add command output gradually.
I can use variables for meaningful repeated values.
I can use here documents for readable text blocks.
I can test after every small change.
I can explain every line of the project.
```
