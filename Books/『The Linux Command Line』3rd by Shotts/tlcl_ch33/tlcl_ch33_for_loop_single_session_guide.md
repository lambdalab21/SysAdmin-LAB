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

1. What does the word after `for` represent? The loop variable. 
2. What does the list after `in` represent? The items to iterate over. 
3. What happens between `do` and `done`? The commands that run once per item. 
4. How many times does the body run if the list has five items? Five. 
5. What is the value of the loop variable on the first pass? First item in the list. 
6. What is the value of the loop variable on the last pass? Last item in the list. 
7. Why is it dangerous to write a loop before knowing what the list contains? Because the list may expand to unexpected items, leading to harmful actions. 

---

# Exercise 1: Loop over a simple list
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

1. How many times did the loop body run? 3 times. 
2. What was `$host` on the first pass? App01. 
3. What was `$host` on the second pass? Db01. 
4. What was `$host` on the third pass? Playground01. 
5. What would change if the list had five names? The body would run five times. 

---

# Exercise 2: The loop variable is not special
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

---
Only after previewing, run:

```bash
for file in *.log; do
    echo "--- $file ---"
    head -n 2 "$file"
done
```

## Inspect

Answer:

1. What did `*.log` become? To protect against word-splitting or globbing if the name contains spaces or special characters. 
2. Did the loop see the literal text `*.log`, or the expanded filenames? One filename. 
3. Why is `"$file"` quoted even though the sample filenames have no spaces? The purpose of the variable would be unclear. 
4. What dangerous command should you never put into a glob loop before previewing? It clearly indicates each value is a filename. 

---

# Exercise 4: Loop over command output carefully

## Run

```bash
for host in $(cat hosts.txt); do
    echo "Would check: $host"
done
```

## Inspect

Answer:

1. How many times did the loop run? 3 times. 
2. What created the list? Command substitution of cat hosts.txt
3. What would happen if `hosts.txt` contained `web server` as one line? It would split into two iterations: "web" and "server". 
4. Why is this not the safest pattern for arbitrary filenames? It performs word-splitting and doesn't handle spaces or special characters safely. 

## Safer contrast: reading lines

This belongs more naturally with `while read`, but compare the behavior:

```bash
while IFS= read -r host; do
    echo "Would check: $host"
done < hosts.txt
```

Answer:

1. Which form reads one line at a time? `while IFS= read -r`
2. Which form splits on shell words? `for item in $(cat file)`
3. When would `while IFS= read -r` be better than `for item in $(cat file)`? When lines may contain spaces or when you need exact line-by-line processing. 

---

# Exercise 5: Loop over script arguments using `"$@"`

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

## Run

```bash
./show-args.sh app01 db01
./show-args.sh "web server" "db server"
./show-args.sh
```

## Inspect

Answer:

1. Why is `"$@"` better than `$@`?It preserves each argument as a single item. 
2. How many arguments were processed in the second run? 2
3. Did `web server` stay one argument? Yes 
4. What happens when there are no arguments? The loop body doesn't run. 

---

# Exercise 6: C-style counter loop

## Run

```bash
for (( i=1; i<=5; i=i+1 )); do
    echo "Pass $i"
done
```

## Inspect

Answer:

1. What was `i` on the first pass? 1. 
2. Why did the loop stop after 5? The condition i<=5 became false. 
3. What changed after each pass? i was incremented by 1. 
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

1. What changed? Start value, step size, and printed values. 
2. Why did it print even numbers? The update added 2 each time, starting from an even number. 
3. What is the stop case? When i exceeds 10. 

---

# Exercise 7: Traditional `for` vs C-style `for`
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

1. Why did the script test `[[ -e "$file" ]]`? To detect the case when the glob matches nothing. 
2. What does the loop variable `file` contain on each pass? Each matching .log filename. 
3. Why does `report_one_file` receive `"$file"` as an argument? So that the function recieves the filename as $1 and can treat it as a local parameter. 
4. What would break if we removed quotes around `$file`? Word-splitting or globbing could occur on the filename. 

---

# Final concept questions

Answer in writing:

1. What is the basic structure of traditional shell `for`? `for variable in list; do commands; done`
2. What is the basic structure of C-style `for`? `for ((init; condition; update)); do commands; done`
3. What is a loop variable? The name that is assigned each successive item from the list. 
4. What is the list in a traditional `for` loop? The sequence of items after `in`
5. How does a glob become a list? The shell expands the pattern into matching filenames before the loop starts. 
6. Why should a glob be previewed before dangerous commands? The expansion may include unexpected files so a dangerous command could act on the wrong things. 
7. Why should loop variables usually be quoted? To protect against word-splitting and pathname expansion. 
8. What does `for arg in "$@"` preserve? Each original argument has a separate intact item. 
9. When is `for item in $(cat file)` acceptable? When the file contains only simple, space-free words and you accept the word-splitting. 
10. When is `while IFS= read -r line` safer? When you need exact lines or safer handling of arbitrary text. 
11. What are the three parts of a C-style loop? Initialization, condition, and update. 
12. What can cause an infinite loop? A condition that never becomes false. 
13. How do you choose between traditional `for` and C-style `for`? Use traditional when you have a list of items. Use C-style for numeric counting. 