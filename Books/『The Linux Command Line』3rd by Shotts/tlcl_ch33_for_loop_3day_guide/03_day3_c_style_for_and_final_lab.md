# TLCL Chapter 33: Flow Control — Looping with `for`

Use this chapter to learn how to repeat work over a list.

The point is not to memorize syntax. The point is to think clearly about:

```text
What is my list?
What is one item?
What happens once per item?
What could go wrong if the list is not what I think it is?
```

Feynman analogy:

```text
A for loop is like a teacher handing out one worksheet to each student.
The class list is the input list.
The current student is the loop variable.
The repeated instruction is the loop body.
The teacher should know who is on the list before starting.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch33-for
cd ~/tlcl-ch33-for
```

---
# Day 3: C-Style `for`, Numeric Loops, and Final Lab

## Read before exercises

Read these Chapter 33 sections:

```text
for: C Language Form
Summing Up
```

## What he should gain

He should gain this distinction:

```text
Traditional for loop: repeat over a list of items.
C-style for loop: repeat with a numeric counter.
```

Most shell work should use the traditional form. Use the C-style form when counting is the real job.

---

# After reading: concept questions

Answer before typing:

1. What are the three parts inside `for (( ... ; ... ; ... ))`?
2. Which part runs before the loop begins?
3. Which part is the condition for continuing?
4. Which part changes the counter after each pass?
5. Why is C-style `for` useful for numbers?
6. Why is traditional `for` better for filenames and arguments?
7. What mistake could create an infinite loop?
8. How can you test a numeric loop safely?

Do not continue until these are answered.

---

# Exercise 1: Trace a C-style loop

## Read before this exercise

Reread **`for`: C Language Form**.

## Skill being gained

He is learning to trace numeric loops exactly.

## Do not type yet

Predict output:

```bash
for (( i=1; i<=3; i++ )); do
    echo "i=$i"
done
```

Fill in:

```text
Before first pass: i = _____
Condition checked: _____
Pass 1 output: _____
After pass 1, i becomes: _____
Pass 2 output: _____
After pass 2, i becomes: _____
Pass 3 output: _____
After pass 3, i becomes: _____
Why does pass 4 not run?
```

## Run

```bash
for (( i=1; i<=3; i++ )); do
    echo "i=$i"
done
```

## Read after this exercise

Reread the syntax and label the three parts:

```text
initialization:
condition:
update:
```

## Explain-back

Explain the loop as a sentence:

```text
Start i at 1. Keep looping while ____. After each pass, ____.
```

---

# Exercise 2: Choose traditional or C-style loop

## Read before this exercise

Review both Chapter 33 sections:

```text
for: Traditional Shell Form
for: C Language Form
```

## Skill being gained

He is learning to choose the right loop form instead of using whichever syntax he remembers.

## Decide first

For each job, choose traditional or C-style:

1. Process `*.log` files.
2. Print numbers 1 through 10.
3. Process script arguments.
4. Try three retries.
5. Check hosts `app01 db01 playground01`.
6. Generate numbered report sections 1 through 5.

Write why.

## Run examples

Traditional list loop:

```bash
for host in app01 db01 playground01; do
    printf 'Host: %s\n' "$host"
done
```

C-style numeric loop:

```bash
for (( retry=1; retry<=3; retry++ )); do
    printf 'Retry attempt: %d\n' "$retry"
done
```

## Read after this exercise

Reread **Summing Up** and write one sentence explaining when each form is appropriate.

## Explain-back

Complete:

```text
Use traditional for when ____.
Use C-style for when ____.
```

---

# Exercise 3: Add a host loop to the report project

## Read before this exercise

Review Chapters 25, 26, 27, 30, and 33 briefly:

```text
Ch 25: build a report gradually
Ch 26: use functions
Ch 27: test conditions
Ch 30: troubleshoot carefully
Ch 33: repeat work over a list
```

## Skill being gained

He is integrating `for` into a real script while preserving disciplined structure.

## Create script

```bash
cat > lab-host-report.sh <<'EOF'
#!/usr/bin/env bash

# lab-host-report.sh - demonstrate a safe host loop

report_header () {
    echo "Lab Host Report"
    echo "==============="
}

report_host () {
    local host="$1"
    echo
    echo "Host: $host"
    echo "Would run: ssh $host hostname"
    echo "Would run: ssh $host uptime"
}

report_footer () {
    echo
    echo "Report complete."
}

report_header

for host in app01 db01 playground01; do
    report_host "$host"
done

report_footer
EOF
```

Run:

```bash
bash lab-host-report.sh
```

## Read after this exercise

Reread the traditional `for` section and Chapter 26 shell functions if necessary. Identify how the loop passes data into the function.

## Explain-back

Answer:

1. What is the list?
2. What is one item?
3. What is the function called on each pass?
4. Why is `report_host "$host"` quoted?
5. Why does `report_host` use `local host="$1"`?
6. Why does the script say `Would run` instead of actually running `ssh`?

---

# Exercise 4: Final lab — argument-aware batch preview script

## Read before this exercise

Review Chapter 32 on `"$@"` and Chapter 33 on `for` loops.

## Skill being gained

He is learning to write a script that processes user-supplied items safely.

## Goal

Create a script that accepts any number of filenames or hostnames and previews processing them.

## Do not type yet

Write the contract:

```text
Script name:
Expected arguments:
What happens if no arguments are provided?
What is one item?
What command repeats?
What dangerous action is avoided?
```

## Create script

```bash
cat > batch-preview.sh <<'EOF'
#!/usr/bin/env bash

# batch-preview.sh - safely preview processing each argument

usage () {
    echo "Usage: $0 ITEM..." >&2
}

if [[ $# -eq 0 ]]; then
    usage
    exit 1
fi

for item in "$@"; do
    printf 'Would process: <%s>\n' "$item"
done
EOF
```

Test no arguments:

```bash
bash batch-preview.sh
echo "exit status=$?"
```

Test normal arguments:

```bash
bash batch-preview.sh app01 db01 playground01
```

Test argument with spaces:

```bash
bash batch-preview.sh "two words.txt" "another item"
```

Make executable:

```bash
chmod +x batch-preview.sh
./batch-preview.sh alpha beta
```

## Read after this exercise

Reread Chapter 33 **Summing Up**. Then review Chapter 32 if `"$@"` is still unclear.

## Explain-back

Answer:

1. Why does the script check `$#` before the loop?
2. Why does `usage` write to stderr?
3. Why does it use `exit 1` for missing arguments?
4. Why does the loop use `"$@"`?
5. Why does the print statement use `<%s>`?
6. What would be dangerous if this script used `rm "$item"` instead of `printf`?

---

# Final self-test

Write answers without notes:

1. What is the difference between a traditional `for` loop and a C-style `for` loop?
2. What is the list in `for file in *.txt`?
3. Who expands `*.txt`?
4. Why should variables inside the loop usually be quoted?
5. What is wrong with `for file in $(ls)`?
6. Why is `"$@"` important?
7. How do you preview a loop before doing real work?
8. When is a numeric counter loop appropriate?
9. What can create an infinite loop?
10. Explain `for host in app01 db01; do report_host "$host"; done` in plain English.

---

# Day 3 finish standard

He is done with Chapter 33 only if he can say:

```text
A for loop repeats work over a list.
I can identify the list before running the loop.
I can trace every pass.
I know when a glob becomes a filename list.
I quote loop variables.
I use "$@" for arguments.
I know when to use traditional for and when to use C-style for.
I preview before doing batch actions.
```
