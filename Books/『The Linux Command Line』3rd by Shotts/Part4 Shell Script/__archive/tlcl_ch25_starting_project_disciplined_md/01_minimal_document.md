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
# Session 1: First Stage — Minimal Document

## Section to read before exercises

Read only:

```text
First Stage: Minimal Document
```

Do not read ahead yet.

## What he should gain from this section

He should learn this idea:

```text
A project should begin with the smallest working version.
```

The first script does not need to be useful yet. It needs to run.

## Feynman analogy

Imagine building a house.

A careless beginner starts by buying furniture, paint, and decorations.

A disciplined builder first asks:

```text
Can I make a small frame that stands up?
```

A script project is similar.

The minimal script is the frame. It proves that:

```text
the file exists
the shebang is correct
the script runs
the output appears
```

## After reading: concept questions

Answer without looking back:

1. Why should the first version be minimal?
2. What is the danger of adding too much too soon?
3. What does “working” mean at this stage?
4. How does this connect to Chapter 24?
5. Why should he test the script before improving it?

## Exercise 1: Write the purpose first

Before typing a script, write this in `work/project-notes.md`:

```bash
cat > work/project-notes.md <<'EOF'
# Chapter 25 Project Notes

Purpose of first script:

Expected first output:

How I will test it:
EOF
```

Fill in the blanks using a text editor.

Do not continue until the purpose is written.

## Exercise 2: Create the minimal script

Create:

```bash
cd ~/tlcl-ch25-project
nano bin/sys_report_page
```

Type:

```bash
#!/usr/bin/env bash

# Program to output a system information page

echo "<html>"
echo "</html>"
```

## Predict before running

Answer:

```text
What program will interpret this file?
How many lines should it print?
Will it print to the screen or a file?
Is it executable yet?
```

## Run safely first with bash

```bash
bash bin/sys_report_page
```

Then make it executable:

```bash
chmod 755 bin/sys_report_page
./bin/sys_report_page
```

## Verify

```bash
./bin/sys_report_page > work/report.html
cat work/report.html
```

Open with text tools first:

```bash
head work/report.html
wc -l work/report.html
```

## Explain every line

Write in `work/project-notes.md`:

```text
Line 1 does...
The comment does...
The first echo does...
The second echo does...
Redirection to work/report.html does...
```

## Disciplined-thinking checkpoint

He is done only if he can say:

```text
I know why the first version is small.
I know how to run it with bash.
I know how to run it as an executable.
I know how to redirect its output to a file.
I can explain every line.
```
