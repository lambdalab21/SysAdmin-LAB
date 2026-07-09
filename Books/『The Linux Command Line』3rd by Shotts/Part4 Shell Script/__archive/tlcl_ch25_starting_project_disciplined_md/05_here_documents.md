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
# Session 5: Here Documents

## Section to read before exercises

Read only:

```text
Here Documents
```

## What he should gain from this section

He should learn:

```text
A here document lets a script feed multiple lines of text to a command cleanly.
```

For this chapter, the main use is generating multi-line HTML without repeating `echo` on every line.

## Feynman analogy

Using many `echo` commands is like handing someone one sentence at a time.

A here document is like handing them a whole page.

```bash
cat <<EOF
many
lines
of
text
EOF
```

means:

```text
Give all these lines to cat as input until the ending marker appears.
```

## After reading: concept questions

1. What does `<<` mean?
2. What is the ending marker for?
3. Why is a here document cleaner than many `echo` lines?
4. Do variables expand inside a normal here document?
5. What could go wrong if the ending marker is misspelled?

## Exercise 1: Small here document

Run:

```bash
cat <<EOF
one
two
three
EOF
```

Predict first:

```text
What command receives the text?
When does the input stop?
What appears on stdout?
```

## Exercise 2: Rewrite the report script with a here document

First save current version:

```bash
cd ~/tlcl-ch25-project
cp bin/sys_report_page versions/sys_report_page_before_here_doc
```

Edit:

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

cat <<EOF
<html>
<head>
  <title>$TITLE</title>
</head>
<body>
  <h1>$TITLE</h1>
  <p>Generated: $CURRENT_TIME</p>
  <p>Host: $HOST_NAME</p>
</body>
</html>
EOF
```

## Predict before running

Answer:

```text
Which command outputs the HTML now?
Will $TITLE expand?
Will CURRENT_TIME expand?
How many echo commands remain?
```

## Run and compare

```bash
bash bin/sys_report_page > work/report.html
cat work/report.html
```

Compare with previous version if available:

```bash
bash versions/sys_report_page_before_here_doc > work/report_old_style.html
diff work/report_old_style.html work/report.html || true
```

Some difference in time is normal.

## Exercise 3: Quoted here document marker

Run:

```bash
TITLE="Example"
cat <<EOF
Title is $TITLE
EOF

cat <<'EOF'
Title is $TITLE
EOF
```

Explain:

```text
Unquoted EOF allows expansion.
Quoted 'EOF' prevents expansion.
```

## Disciplined-thinking checkpoint

He is done only if he can answer:

1. What does a here document feed input to?
2. Why did it simplify the script?
3. Why did variables still expand?
4. What changes if the delimiter is quoted?
5. Why is this easier to maintain than many `echo` lines?
