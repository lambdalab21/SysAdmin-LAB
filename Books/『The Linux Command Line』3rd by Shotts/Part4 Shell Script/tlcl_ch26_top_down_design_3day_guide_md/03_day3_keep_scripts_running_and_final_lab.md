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
# Day 3: Keep Scripts Running and Final Lab

## Read before exercises

Read the Chapter 26 sections:

```text
Keep Scripts Running
Summing Up
```

## What he should gain from this reading

He should gain this idea:

```text
During development, the script should stay runnable.
If a section is unfinished, use a placeholder function.
```

This is a professional habit.

A weak beginner writes half a script and cannot run it.

A disciplined beginner keeps the script runnable after every change.

---

# Before reading: Feynman preview

Imagine building a bridge.

A reckless builder removes the old bridge before the new bridge can hold weight.

A disciplined builder keeps a safe path open at every stage.

In scripting:

```text
The script should keep running while you improve it.
```

Placeholders are not laziness. They are scaffolding.

---

# After reading: concept questions

Answer without looking back:

1. Why should a script keep running during development?
2. What is a placeholder or stub function?
3. How does top-down design help you test early?
4. Why is one small change safer than five changes at once?
5. What should you do immediately after changing a function?
6. What does Chapter 26 add to Chapters 24 and 25?

---

# Exercise 1: Use placeholder functions deliberately

## Skill being gained

He is learning that placeholders keep the script runnable.

## Predict before typing

Answer:

```text
Which function is unfinished?
What should it print for now?
How will I know the script still runs?
```

## Add or replace home-space function

In `lab-report.sh`, make this placeholder if not finished:

```bash
report_home_space () {
    echo "<h2>Home Directory</h2>"
    echo "<p>Home directory report is not finished yet.</p>"
}
```

Run:

```bash
bash lab-report.sh > report.html
grep -A3 "Home Directory" report.html
```

## Explain-back

Answer:

1. Why is this better than leaving the function broken?
2. What does the reader of the report see?
3. What does the developer know still needs work?

---

# Exercise 2: Replace placeholder with real command

## Skill being gained

He is learning safe stepwise refinement.

## Read before exercise

Reread “Keep Scripts Running.”

Ask:

```text
How can I improve one function without breaking the whole script?
```

## Predict before typing

Answer:

```text
What command can estimate home directory usage?
What might be slow or produce errors?
How can I test only this section after running?
```

## Improve the function

Replace `report_home_space` with:

```bash
report_home_space () {
    local title="Home Directory"
    echo "<h2>$title</h2>"
    echo "<pre>"
    du -sh "$HOME" 2>/dev/null
    echo "</pre>"
}
```

Run:

```bash
bash lab-report.sh > report.html
grep -A6 "Home Directory" report.html
```

## Explain-back

Answer:

1. Why is `$HOME` quoted?
2. Why might `du` print errors?
3. What does `2>/dev/null` do?
4. Is hiding errors always a good idea?
5. For learning, how could you inspect the errors instead?

Try:

```bash
du -sh "$HOME"
```

Then compare with:

```bash
du -sh "$HOME" 2>/dev/null
```

---

# Exercise 3: Final cleaned script

## Skill being gained

He is learning to produce a readable script with a clear top-down shape.

Create a final version:

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
    local title="System Information"
    echo "<h2>$title</h2>"
    echo "<pre>"
    hostname
    date
    uname -a
    echo "</pre>"
}

report_disk_space () {
    local title="Disk Space"
    echo "<h2>$title</h2>"
    echo "<pre>"
    df -h
    echo "</pre>"
}

report_home_space () {
    local title="Home Directory"
    echo "<h2>$title</h2>"
    echo "<pre>"
    du -sh "$HOME" 2>/dev/null
    echo "</pre>"
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

Test syntax first:

```bash
bash -n lab-report.sh
```

Run:

```bash
bash lab-report.sh > report.html
```

Inspect:

```bash
head report.html
grep -E "<h1>|<h2>" report.html
tail report.html
```

Make executable:

```bash
chmod +x lab-report.sh
./lab-report.sh > report.html
```

---

# Exercise 4: Break it deliberately and debug

## Skill being gained

He is learning that disciplined thinkers diagnose errors.

Copy the script:

```bash
cp lab-report.sh broken-report.sh
```

Edit `broken-report.sh` and remove one closing `}` from a function.

Then run:

```bash
bash -n broken-report.sh
bash broken-report.sh > broken.html
```

Answer:

1. What error did Bash show?
2. Did `bash -n` catch it?
3. Why is syntax checking useful before running?
4. How would a long script be harder to debug without functions?

Restore by deleting the broken file:

```bash
rm -f broken-report.sh broken.html
```

---

# Final concept questions

Answer in writing:

1. What is top-down design?
2. What is stepwise refinement?
3. What is a shell function?
4. What is a local variable?
5. Why should the script stay runnable while being developed?
6. What is a placeholder function?
7. Why should the main body of the script read like an outline?
8. Why is `bash -n` useful?
9. Why is `bash script.sh > output.html` useful during testing?
10. What did Chapter 26 teach that Chapter 25 did not?

---

# Final Feynman explain-back

Explain the final script to a younger student:

```text
The script creates an HTML report.
The main body calls five functions.
Each function owns one section of the report.
Local variables are used for temporary section titles.
The script prints to stdout, so I can redirect it to report.html.
```

Then explain each function:

```text
report_header:
report_system_info:
report_disk_space:
report_home_space:
report_footer:
```

If he cannot explain a function, he should not move on.

---

# Day 3 finish standard

He is done with Chapter 26 only if he can say:

```text
I can design a script from the top down.
I can use functions to organize sections.
I can keep variables local when they are temporary.
I can keep a script runnable while improving it.
I can test syntax with bash -n.
I can explain every function's job in plain English.
```
