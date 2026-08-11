# TLCL Chapter 33 — Flow Control: Looping with `for`

**Book:** William Shotts, *The Linux Command Line*  
**Chapter:** 33 — Flow Control: Looping with `for`  
**Format:** one focused study session, not split into days

This chapter is short, so do **not** split it into Day 1, Day 2, and Day 3. Treat it as one careful session with several passes:

```text
read
→ answer concepts
→ predict loops before typing
→ run tiny examples
→ inspect output
→ explain what changed each iteration
→ apply to a small script
```

The goal is not to memorize `for` syntax.

The goal is to learn how to think about repeated work.

---

## Why this chapter matters

A shell script becomes useful when it can repeat a task safely.

Without loops, he writes commands like this:

```bash
wc -l app.log
wc -l auth.log
wc -l system.log
```

With a loop, he can express the idea once:

```bash
for file in app.log auth.log system.log; do
    wc -l "$file"
done
```

The important skill is not typing `for`. The important skill is identifying:

```text
What list am I looping over?
What is one item?
What command should happen to each item?
What could go wrong if the list is wrong?
```

Feynman analogy:

```text
A for loop is like giving the same worksheet to each student in a row.
The list is the row of students.
The loop variable is the current student.
The loop body is the task you do for that student.
```

---

## Chapter sections to use

Use the chapter in this order:

```text
1. for: Traditional Shell Form
2. for: C Language Form
3. Summing Up
```

Do not read passively. Every section must answer:

```text
What new kind of repeated work can I express after reading this section?
```

---

# Before touching the keyboard

Before doing any exercise, copy this checklist into your notes.

```text
Loop purpose:
List source:
One item example:
Loop variable name:
Command repeated for each item:
Expected first pass:
Expected last pass:
What could go wrong?
How will I preview safely?
```

He may not run the loop until this is filled in.

This is the anti-mindless-typing rule.

---

# Reading Pass 1: Traditional `for`

## Read before Exercise 1

Read:

```text
for: Traditional Shell Form
```

Read only until you understand the basic structure:

```bash
for variable in list; do
    commands
done
```

## What he should gain

He should gain this mental model:

```text
The shell creates a list.
Each item in the list is assigned to the loop variable.
The commands between do and done run once per item.
```

## Concept questions after reading

Answer without looking back:

1. What does the word after `for` represent?
2. What does the list after `in` represent?
3. What happens between `do` and `done`?
4. How many times does the body run if the list has five items?
5. What is the value of the loop variable on the first pass?
6. What is the value of the loop variable on the last pass?
7. Why is it dangerous to write a loop before knowing what the list contains?

Do not continue until he can answer these.

---

# Exercise 1: Loop over a simple list

## Skill being gained

He is learning the simplest form of repeated work.

## Prediction gate

Before typing, fill this in:

```text
Loop purpose:
List source:
One item example:
Loop variable name:
Expected output lines:
```

## Run

```bash
mkdir -p ~/tlcl-ch33-for-loop
cd ~/tlcl-ch33-for-loop

for host in app01 db01 playground01; do
    echo "Checking host: $host"
done
```

## Inspect

Answer:

1. How many times did the loop body run?
2. What was `$host` on the first pass?
3. What was `$host` on the second pass?
4. What was `$host` on the third pass?
5. What would change if the list had five names?

## Feynman explain-back

Explain the loop to a younger student:

```text
The loop takes one name at a time from the list.
It stores that name in host.
Then it prints a message using the current host.
```

---

# Exercise 2: The loop variable is not special

## Read before exercise

Reread the examples in `for: Traditional Shell Form`.

Pay attention to this idea:

```text
The variable name is chosen by the script writer.
The name should describe one item from the list.
```

## Skill being gained

He is learning to choose clear variable names.

## Prediction gate

Before typing, answer:

```text
Why is file a better variable name than x here?
What is one item in the list?
What command runs for each item?
```

## Create sample files

```bash
printf 'error one\nwarning one\n' > app.log
printf 'login ok\nlogin failed\n' > auth.log
printf 'boot ok\ndisk ok\n' > system.log
```

## Run

```bash
for file in app.log auth.log system.log; do
    echo "File: $file"
    wc -l "$file"
done
```

## Inspect

Answer:

1. Why is `$file` quoted?
2. What does `wc -l "$file"` receive on each pass?
3. What would be harder to understand if the variable were named `x`?
4. Why is `file` a good name here?

## Read after exercise

Reread the same section briefly and look at the book's examples again.

This time ask:

```text
What is the list?
What is one item?
What is the repeated action?
```

---

# Exercise 3: Preview a glob before using it in a loop

## Skill being gained

He is learning that loops over files depend on shell expansion.

A file loop often begins with a glob:

```bash
for file in *.log; do
    command "$file"
done
```

But the shell expands `*.log` before the loop begins.

## Prediction gate

Before typing, answer:

```text
What should *.log expand to in this directory?
How can I preview the expansion safely?
What would happen if no files matched?
Why should I not start with rm, mv, chmod, or cp?
```

## Preview first

```bash
printf '<%s>\n' *.log
```

Only after previewing, run:

```bash
for file in *.log; do
    echo "--- $file ---"
    head -n 2 "$file"
done
```

## Inspect

Answer:

1. What did `*.log` become?
2. Did the loop see the literal text `*.log`, or the expanded filenames?
3. Why is `"$file"` quoted even though the sample filenames have no spaces?
4. What dangerous command should you never put into a glob loop before previewing?

## Feynman explain-back

Explain:

```text
The shell first turns *.log into a list of matching filenames.
Then the for loop walks through that list one filename at a time.
The command inside the loop receives the current filename.
```

---

# Exercise 4: Loop over command output carefully

## Read before exercise

Reread the traditional `for` section again, especially any examples where the list is generated by expansion or command substitution.

## Skill being gained

He is learning that the list can come from a command, but this must be done carefully.

For simple words such as hostnames, this is acceptable:

```bash
for host in $(cat hosts.txt); do
    echo "$host"
done
```

For arbitrary filenames, this style can be unsafe because word splitting can break names containing spaces or unusual characters.

## Create a safe simple input file

```bash
cat > hosts.txt <<'EOF'
app01
db01
playground01
EOF
```

## Prediction gate

Before typing, answer:

```text
What will cat hosts.txt print?
How many loop iterations should happen?
What value should host have on each pass?
Why is this okay for simple hostnames?
Why would this be risky for filenames with spaces?
```

## Run

```bash
for host in $(cat hosts.txt); do
    echo "Would check: $host"
done
```

## Inspect

Answer:

1. How many times did the loop run?
2. What created the list?
3. What would happen if `hosts.txt` contained `web server` as one line?
4. Why is this not the safest pattern for arbitrary filenames?

## Safer contrast: reading lines

This belongs more naturally with `while read`, but compare the behavior:

```bash
while IFS= read -r host; do
    echo "Would check: $host"
done < hosts.txt
```

Answer:

1. Which form reads one line at a time?
2. Which form splits on shell words?
3. When would `while IFS= read -r` be better than `for item in $(cat file)`?

---

# Exercise 5: Loop over script arguments using `"$@"`

## Read before exercise

Before this exercise, review your notes from Shotts Chapter 32:

```text
Positional Parameters
"$@"
```

This exercise connects Chapter 33 to Chapter 32.

## Skill being gained

He is learning how `for` can process command-line arguments safely.

## Create the script

```bash
cat > show-args.sh <<'EOF'
#!/usr/bin/env bash

for arg in "$@"; do
    printf 'Argument: <%s>\n' "$arg"
done
EOF

chmod +x show-args.sh
```

## Prediction gate

Before running, predict each output:

```bash
./show-args.sh app01 db01
./show-args.sh "web server" "db server"
./show-args.sh
```

## Run

```bash
./show-args.sh app01 db01
./show-args.sh "web server" "db server"
./show-args.sh
```

## Inspect

Answer:

1. Why is `"$@"` better than `$@`?
2. How many arguments were processed in the second run?
3. Did `web server` stay one argument?
4. What happens when there are no arguments?

## Feynman explain-back

Explain:

```text
"$@" means each original command-line argument stays separate.
A for loop can process each argument one at a time.
Quoting protects arguments that contain spaces.
```

---

# Reading Pass 2: C language form

## Read before Exercise 6

Read:

```text
for: C Language Form
```

## What he should gain

He should gain this idea:

```text
The C-style for loop is useful when the loop is controlled by a number.
```

Traditional shell form asks:

```text
What list of items do I have?
```

C-style form asks:

```text
What counter starts where?
When should it stop?
How does it change each pass?
```

## Concept questions after reading

Answer without looking back:

1. How is the C-style `for` different from traditional shell `for`?
2. What are the three parts inside `(( ... ))`?
3. Which part initializes the counter?
4. Which part tests whether the loop should continue?
5. Which part changes the counter?
6. When is C-style `for` clearer than traditional `for`?
7. When is traditional `for` clearer than C-style `for`?

---

# Exercise 6: C-style counter loop

## Skill being gained

He is learning numeric repetition.

## Prediction gate

Before typing, fill this in:

```text
Starting value:
Continue condition:
Change after each pass:
Expected first output:
Expected last output:
Number of total passes:
```

## Run

```bash
for (( i=1; i<=5; i=i+1 )); do
    echo "Pass $i"
done
```

## Inspect

Answer:

1. What was `i` on the first pass?
2. Why did the loop stop after 5?
3. What changed after each pass?
4. What would happen if the condition were `i<5`?
5. What would happen if the update were missing?

## Modify one thing only

Change only the update expression:

```bash
for (( i=2; i<=10; i=i+2 )); do
    echo "Even number: $i"
done
```

Answer:

1. What changed?
2. Why did it print even numbers?
3. What is the stop case?

---

# Exercise 7: Traditional `for` vs C-style `for`

## Skill being gained

He is learning to choose the right loop form.

## Prediction gate

Before running, decide which loop form is clearer.

Task A:

```text
Print a report section for each log file.
```

Task B:

```text
Print numbers 1 through 10.
```

Task C:

```text
Process each command-line argument.
```

Task D:

```text
Repeat exactly 3 times.
```

## Write answers

```text
Task A loop form:
Reason:

Task B loop form:
Reason:

Task C loop form:
Reason:

Task D loop form:
Reason:
```

## Check against these examples

Traditional list loop:

```bash
for file in *.log; do
    wc -l "$file"
done
```

C-style counter loop:

```bash
for (( i=1; i<=10; i++ )); do
    echo "$i"
done
```

Arguments loop:

```bash
for arg in "$@"; do
    echo "$arg"
done
```

Exact repetition:

```bash
for (( i=1; i<=3; i++ )); do
    echo "Try $i"
done
```

---

# Final lab: Add file-loop reporting to a script

## Read before final lab

Reread:

```text
for: Traditional Shell Form
for: C Language Form
```

Then skim:

```text
Summing Up
```

Ask:

```text
What kind of repeated work does this chapter let me add to my scripts?
```

## Skill being gained

He is learning to use `for` inside a useful script, not only at the prompt.

## Create the script

```bash
cat > log-summary.sh <<'EOF'
#!/usr/bin/env bash

# log-summary.sh - summarize .log files in the current directory

report_header () {
    echo "Log Summary"
    echo "==========="
}

report_one_file () {
    local file="$1"
    echo
    echo "File: $file"
    echo "Lines: $(wc -l < "$file")"
    echo "First line: $(head -n 1 "$file")"
}

report_all_logs () {
    local file

    for file in *.log; do
        if [[ -e "$file" ]]; then
            report_one_file "$file"
        else
            echo "No .log files found."
        fi
    done
}

report_header
report_all_logs
EOF

chmod +x log-summary.sh
```

## Prediction gate

Before running, answer:

```text
What should *.log expand to?
What function owns one file?
What function owns the whole loop?
What happens if there are no .log files?
Why is [[ -e "$file" ]] included?
```

## Run

```bash
./log-summary.sh
```

## Test the no-match case

Create a temporary empty directory:

```bash
mkdir -p empty-test
cp log-summary.sh empty-test/
cd empty-test
./log-summary.sh
cd ..
```

## Inspect

Answer:

1. Why did the script test `[[ -e "$file" ]]`?
2. What does the loop variable `file` contain on each pass?
3. Why does `report_one_file` receive `"$file"` as an argument?
4. What would break if we removed quotes around `$file`?
5. How does this final script connect Chapters 26, 27, 30, 32, and 33?

Connections:

```text
Ch 26: functions organize the script
Ch 27: if checks whether a matched file exists
Ch 30: defensive programming handles no-match case
Ch 32: report_one_file uses $1
Ch 33: for repeats over log files
```

---

# Final concept questions

Answer in writing:

1. What is the basic structure of traditional shell `for`?
2. What is the basic structure of C-style `for`?
3. What is a loop variable?
4. What is the list in a traditional `for` loop?
5. How does a glob become a list?
6. Why should a glob be previewed before dangerous commands?
7. Why should loop variables usually be quoted?
8. What does `for arg in "$@"` preserve?
9. When is `for item in $(cat file)` acceptable?
10. When is `while IFS= read -r line` safer?
11. What are the three parts of a C-style loop?
12. What can cause an infinite loop?
13. How do you choose between traditional `for` and C-style `for`?
14. What did Chapter 33 add to the scripts from Chapters 24–32?

---

# Final Feynman explanation

Explain Chapter 33 in plain English:

```text
A for loop repeats a command for each item in a list.
In the traditional form, the shell gives the loop one item at a time.
In the C-style form, a counter controls how many times the loop runs.
Before writing a loop, I must know where the list comes from, what one item looks like, and what command should happen to each item.
```

Then explain this script line by line:

```bash
for file in *.log; do
    wc -l "$file"
done
```

He is ready to move on only if he can explain:

```text
what *.log becomes
what file means
why "$file" is quoted
how many times the loop runs
what command runs each time
what could go wrong
```

---

# Retention drills

Do these the next day, then again one week later.

## Drill 1

Predict before running:

```bash
for x in a b c; do echo "$x"; done
```

## Drill 2

Predict before running:

```bash
for n in 1 2 3; do echo $((n * n)); done
```

## Drill 3

Preview first, then run:

```bash
printf '<%s>\n' *.log
for file in *.log; do echo "$file"; done
```

## Drill 4

Explain why this is safer:

```bash
for arg in "$@"; do
    printf '<%s>\n' "$arg"
done
```

## Drill 5

Explain the three parts:

```bash
for (( i=1; i<=5; i++ )); do
    echo "$i"
done
```

---

# Completion standard

He has completed Chapter 33 only when he can say:

```text
I know whether I am looping over words, filenames, arguments, or numbers.
I can preview a glob before using it.
I quote loop variables.
I can choose traditional for or C-style for based on the task.
I can explain the first pass, last pass, and stop condition.
I can use for loops inside a real script without guessing.
```
