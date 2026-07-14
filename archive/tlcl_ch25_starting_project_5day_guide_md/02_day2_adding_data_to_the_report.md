#Book/The-Linux-Command-Line Author/Shotts 
#shell-script
# Author/Shotts Chapter 25: Starting a Project

This guide is for Chapter 25, **Starting a Project**, from William Author/Shotts's *The Linux Command Line*.

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
# Day 2: Second Stage — Adding a Little Data

## Section to read before exercises

Read Chapter 25 section:

```text
Second Stage: Adding a Little Data
```

## What he should be gaining

He should learn this habit:

```text
Add real data gradually, and keep the report readable.
```

This is where a script becomes useful, but also where mindless typing becomes dangerous. He must know where command output goes.

---

# Before reading: Feynman preview

A report generator is like a student writing a lab report.

The report needs:

```text
title
section heading
data
a way to make the data readable
```

For command output, HTML often needs:

```html
<pre>
...
</pre>
```

because command output has spacing and columns.

---

# After reading: concept questions

Answer without looking back:

1. Why does the script use commands such as `hostname`, `date`, `uname`, or `df`?
2. Why might command output need `<pre>` tags in HTML?
3. What is stdout?
4. If the whole script is redirected to a file, where does each `echo` output go?
5. Where does output from `df -h` go?
6. Why should he add one command at a time?

---

# Exercise 1: Add system identity data

## Skill being trained

He is learning to embed command output in a generated report.

## Predict before typing

Answer:

```text
Which command prints the hostname?
Which command prints the current date?
Where should this output appear in the HTML file?
```

## Edit the script

Replace the body area with a system-information section like this:

```bash
cat > sys_info_page.sh <<'EOF'
#!/usr/bin/env bash

# sys_info_page.sh - generate a simple system information page

echo "<html>"
echo "<head>"
echo "<title>System Information Report</title>"
echo "</head>"
echo "<body>"
echo "<h1>System Information Report</h1>"

echo "<h2>System Identity</h2>"
echo "<pre>"
hostname
date
uname -a
echo "</pre>"

echo "</body>"
echo "</html>"
EOF
```

Run:

```bash
bash sys_info_page.sh > sys_info_page.html
less sys_info_page.html
```

## Explain-back

Answer:

1. Which lines print HTML tags?
2. Which lines run system commands?
3. Why does command output appear inside the HTML file?
4. What does `<pre>` help preserve?

---

# Exercise 2: Add disk information

## Skill being trained

He is learning to add one useful report section without breaking the whole script.

## Read before exercise

Reread the part of the chapter where Author/Shotts adds more data to the report.

Ask:

```text
What data is useful enough to deserve a report section?
How can I keep the output readable?
```

## Predict before typing

Answer:

```text
What does df -h show?
Will it print to stdout or stderr?
Where should it appear in the report?
```

## Add this section before `</body>`

```bash
echo "<h2>Disk Space</h2>"
echo "<pre>"
df -h
echo "</pre>"
```

Run and inspect:

```bash
bash sys_info_page.sh > sys_info_page.html
grep -A10 "Disk Space" sys_info_page.html
```

## Explain-back

Answer:

1. What did `df -h` contribute?
2. Why did you use `grep -A10` for inspection?
3. Did the script remain runnable?
4. What would be the next smallest useful section?

---

# Exercise 3: Do not hide from errors

## Skill being trained

He is learning to distinguish stdout and stderr.

Try:

```bash
ls /does-not-exist > test-output.txt
cat test-output.txt
```

Answer:

1. Did the error go into the file?
2. Where did the error appear?
3. Why does this matter when writing scripts?

Then try:

```bash
ls /does-not-exist > test-output.txt 2> test-error.txt
cat test-output.txt
cat test-error.txt
rm -f test-output.txt test-error.txt
```

## Explain-back

Say:

```text
stdout and stderr are separate streams.
A report script usually captures stdout, but errors may still appear on the terminal unless redirected.
```

---

# Day 2 finish standard

He is done only if he can say:

```text
I can add useful command output to a generated report.
I understand why <pre> is useful.
I know where stdout goes when the script is redirected.
I know stderr is separate.
I can add one report section and test it before adding another.
```
