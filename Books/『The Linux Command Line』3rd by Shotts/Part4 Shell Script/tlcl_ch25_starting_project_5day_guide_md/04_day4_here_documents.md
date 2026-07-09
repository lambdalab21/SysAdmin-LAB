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
# Day 4: Here Documents

## Section to read before exercises

Read Chapter 25 section:

```text
Here Documents
```

## What he should be gaining

He should learn this habit:

```text
When a script needs to output a block of text, use a structure that keeps the block readable.
```

Here documents are useful when many `echo` lines become noisy.

---

# Before reading: Feynman preview

A series of many `echo` lines is like reading a paragraph one word at a time.

A here document says:

```text
Everything from here until this marker is input to the command.
```

Example idea:

```bash
cat <<EOF
many lines here
EOF
```

The marker is like a fence around a block of text.

---

# After reading: concept questions

Answer without looking back:

1. What is a here document?
2. What command commonly receives the here document in this chapter?
3. What does the delimiter such as `EOF` do?
4. Why must the ending delimiter appear correctly?
5. Are variables expanded inside an unquoted here document delimiter?
6. Why can a here document make HTML output easier to read?
7. When might `echo` still be simpler?

---

# Exercise 1: Replace many echo lines with a here document

## Skill being trained

He is learning to output structured text more readably.

## Predict before typing

Answer:

```text
What block of text will cat print?
Where will $TITLE be expanded?
What file will receive the script output?
```

## Rewrite the script header with a here document

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

echo "<h2>System Identity</h2>"
echo "<pre>"
hostname
date
uname -a
echo "</pre>"

echo "<h2>Disk Space</h2>"
echo "<pre>"
df -h
echo "</pre>"

cat <<HTML
</body>
</html>
HTML
EOF
```

Check and run:

```bash
bash -n sys_info_page.sh
bash sys_info_page.sh > sys_info_page.html
head sys_info_page.html
```

## Explain-back

Answer:

1. What command receives the here document?
2. Where does the here document begin?
3. Where does it end?
4. Why did `$TITLE` expand inside the output?
5. Why is this easier to read than many `echo` lines?

---

# Exercise 2: Break the delimiter deliberately

## Skill being trained

He is learning how here-document mistakes fail.

Copy the script:

```bash
cp sys_info_page.sh broken-heredoc.sh
```

Edit `broken-heredoc.sh` and change the final delimiter from:

```text
HTML
```

to:

```text
HTM
```

Run:

```bash
bash -n broken-heredoc.sh
bash broken-heredoc.sh > broken.html
```

Answer:

1. What warning or error did Bash show?
2. Did `bash -n` help?
3. Why must the ending delimiter match exactly?
4. Why is this useful to learn deliberately?

Clean up:

```bash
rm -f broken-heredoc.sh broken.html
```

---

# Exercise 3: Quoted versus unquoted delimiter

## Skill being trained

He is learning whether variables expand inside a here document.

Try:

```bash
cat > heredoc-expansion-test.sh <<'EOF'
#!/usr/bin/env bash

NAME="Alice"

cat <<BLOCK
Hello $NAME
BLOCK

cat <<'BLOCK'
Hello $NAME
BLOCK
EOF

bash heredoc-expansion-test.sh
rm -f heredoc-expansion-test.sh
```

## Explain-back

Answer:

1. Which block expanded `$NAME`?
2. Which block printed `$NAME` literally?
3. Why might quoted delimiters be useful?
4. In the report script, do we want `$TITLE` expanded or literal?

---

# Day 4 finish standard

He is done only if he can say:

```text
A here document sends a block of text to a command.
The delimiter marks where the block begins and ends.
Unquoted delimiters allow variable expansion.
Quoted delimiters suppress expansion.
Here documents can make structured output easier to read.
```
