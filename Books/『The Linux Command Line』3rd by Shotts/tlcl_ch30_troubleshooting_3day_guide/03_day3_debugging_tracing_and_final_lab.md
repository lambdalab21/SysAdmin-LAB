# TLCL Chapter 30: Troubleshooting

This guide is for Chapter 30, **Troubleshooting**, from William Shotts's *The Linux Command Line*.

The goal is not to memorize debugging tricks.

The goal is to build this habit:

```text
Observe the failure.
State what should have happened.
Make one hypothesis.
Use one test.
Inspect evidence.
Change one thing.
Retest.
Explain what happened.
```

Feynman analogy:

```text
Debugging is like fixing a lamp.
A careless person shakes it and hopes it works.
A disciplined thinker asks:
Is there power? Is the bulb good? Is the switch working? Is the wire broken?
One test at a time.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
```

Disciplined rule:

```text
Do not randomly edit a broken script.
Write down what you expected, what actually happened, and what evidence you used.
```

---
# Day 3: Debugging, Tracing, and Final Lab

## Read before exercises

Read these Chapter 30 sections:

```text
Debugging
Finding the Problem Area
Tracing
Examining Values During Execution
Summing Up
```

## What he should gain from this reading

He should gain this idea:

```text
Debugging is controlled investigation.
Do not guess. Narrow the problem area, trace execution, inspect values, and retest.
```

He is learning to use tools such as:

```text
bash -n
bash -x
set -x
set +x
echo / printf debugging
small test cases
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
If a large script fails, do not stare at the whole script.
Find the neighborhood of the problem.
Then inspect the exact line.
Then inspect the exact value.
```

Analogy:

```text
If a house is dark, you do not replace every wire.
You check whether the whole block has power, then the house, then the room, then the lamp.
```

---

# After reading: concept questions

Answer without looking back:

1. What does it mean to find the problem area?
2. Why should a bug be isolated before editing?
3. What does `bash -x script.sh` show?
4. Why is tracing sometimes too noisy?
5. How can `set -x` and `set +x` trace only part of a script?
6. Why should variable values be inspected during execution?
7. Why is `printf` often better than vague `echo` debugging?
8. Why should each fix be followed by retesting?
9. What is the danger of making several changes before retesting?
10. What makes a useful debugging note?

---

# Exercise 1: Trace a simple script

## Skill being gained

He is learning to see what Bash actually executes after expansion.

## Predict before typing

Answer:

```text
What lines do I expect bash -x to show?
Will it show commands before or after variable expansion?
```

## Create script

```bash
cd ~/tlcl-ch30-troubleshooting

cat > trace-demo.sh <<'EOF'
#!/usr/bin/env bash

name="linuxbox"
count=3

echo "Hello, $name"
echo "Count is $count"
EOF
```

Run normally:

```bash
bash trace-demo.sh
```

Run with trace:

```bash
bash -x trace-demo.sh
```

## Explain-back

Answer:

1. What extra information did `bash -x` show?
2. Did it show `$name` literally or expanded?
3. How can this help with debugging?
4. Why might this be too noisy in a long script?

---

# Exercise 2: Trace only the suspicious area

## Skill being gained

He is learning to turn tracing on and off.

## Predict before typing

Answer:

```text
Which part should be traced?
Which part should remain quiet?
```

## Create script

```bash
cat > selective-trace.sh <<'EOF'
#!/usr/bin/env bash

file="/etc/passwd"
pattern="root"

echo "Starting check"

set -x
grep "$pattern" "$file" | head -n 1
set +x

echo "Finished check"
EOF

bash selective-trace.sh
```

## Explain-back

Answer:

1. Which lines were traced?
2. Which lines were not traced?
3. Why is selective tracing better than tracing everything?
4. What command actually ran after variables expanded?

---

# Exercise 3: Examine values during execution

## Skill being gained

He is learning to inspect exact variable values, including spaces and empty strings.

## Predict before typing

Answer:

```text
Why is printf '<%s>
' useful?
What does it show that plain echo may hide?
```

## Create script

```bash
cat > value-debug.sh <<'EOF'
#!/usr/bin/env bash

input="  report file.txt  "
empty=""

printf 'input=<%s>
' "$input"
printf 'empty=<%s>
' "$empty"
printf 'home=<%s>
' "$HOME"
EOF

bash value-debug.sh
```

## Explain-back

Answer:

1. How can angle brackets reveal spaces?
2. How can they reveal empty variables?
3. Why is this useful before using a variable as a filename?

---

# Exercise 4: Final debugging lab

## Skill being gained

He is learning full disciplined debugging: classify, isolate, trace, inspect, fix, retest.

## Create intentionally flawed script

```bash
cat > lab-check.sh <<'EOF'
#!/usr/bin/env bash

# lab-check.sh - intentionally flawed system check script

target_dir=$1
pattern=$2

report_header () {
    echo "Lab Check Report"
    echo "================"
}

check_directory () {
    if [[ -d $target_dir ]]; then
        echo "Directory: $target_dir"
    else
        echo "Directory not found: $target_dir"
    fi
}

search_logs () {
    echo "Searching for pattern: $pattern"
    grep -R $pattern $target_dir | head
}

report_header
check_directory
search_logs
EOF
```

## Do not run yet

Before running, answer:

```text
What arguments does this script expect?
What should happen with good input?
What should happen with missing input?
What variables are unquoted?
What commands might fail?
What filenames or patterns might be dangerous?
```

## Run test cases

Create a safe lab:

```bash
mkdir -p sample-logs
cat > sample-logs/app.log <<'EOF'
INFO startup complete
ERROR disk almost full
WARN retrying request
EOF

cat > 'sample-logs/two words.log' <<'EOF'
ERROR filename contains spaces
EOF
```

Run:

```bash
bash -n lab-check.sh
bash lab-check.sh sample-logs ERROR
bash lab-check.sh sample-logs
bash lab-check.sh /no/such/dir ERROR
bash -x lab-check.sh sample-logs ERROR
```

## Debugging log

For each problem found, fill this out:

```text
Expected behavior:
Actual behavior:
Bug type:
Smallest suspicious area:
Hypothesis:
Test used:
Evidence:
Fix:
Retest:
Feynman explanation:
```

## Improve the script

After diagnosing, replace it with this safer version:

```bash
cat > lab-check.sh <<'EOF'
#!/usr/bin/env bash

# lab-check.sh - safer system check script

set -u
set -o pipefail

target_dir=${1:-}
pattern=${2:-}

usage () {
    echo "Usage: lab-check.sh DIRECTORY PATTERN" >&2
}

report_header () {
    echo "Lab Check Report"
    echo "================"
}

verify_input () {
    if [[ -z "$target_dir" || -z "$pattern" ]]; then
        usage
        exit 2
    fi

    if [[ ! -d "$target_dir" ]]; then
        echo "Error: '$target_dir' is not a directory" >&2
        exit 1
    fi
}

check_directory () {
    echo "Directory: $target_dir"
}

search_logs () {
    echo "Searching for pattern: $pattern"
    grep -R -- "$pattern" "$target_dir" | head
}

verify_input
report_header
check_directory
search_logs
EOF
```

Retest:

```bash
bash -n lab-check.sh
bash lab-check.sh sample-logs ERROR
bash lab-check.sh sample-logs
bash lab-check.sh /no/such/dir ERROR
bash lab-check.sh 'sample-logs' 'disk almost'
```

## Explain-back

Answer:

1. Why use `${1:-}` instead of `$1` with `set -u`?
2. Why validate before searching?
3. Why quote `"$target_dir"` and `"$pattern"`?
4. Why use `--` before grep arguments?
5. Why is `usage` a function?
6. Which failures now produce clear messages?
7. Which test cases prove the script is safer?

---

# Exercise 5: Write a troubleshooting report

## Skill being gained

He is learning to communicate debugging clearly.

Write a short report:

```text
Bug 1:
Expected:
Actual:
Cause:
Fix:
Evidence after retest:

Bug 2:
Expected:
Actual:
Cause:
Fix:
Evidence after retest:

Most important lesson:
```

Rules:

```text
No vague words: “weird,” “broken,” “didn't work.”
Use evidence: command, output, exit status, trace line, variable value.
```

---

# Final concept questions

Answer in writing:

1. What is the difference between syntax, expansion, and logical errors?
2. Why should `bash -n` be run before testing a changed script?
3. What does `bash -x` show?
4. Why does quoting variables prevent many bugs?
5. Why can filenames be dangerous?
6. What does `--` protect against?
7. Why should error messages go to stderr?
8. Why should bad input be tested intentionally?
9. Why is one change at a time better than many changes at once?
10. What makes a debugging explanation convincing?

---

# Day 3 finish standard

He is done with Chapter 30 only if he can say:

```text
I can classify a bug.
I can isolate a problem area.
I can use bash -n for syntax checking.
I can use bash -x or set -x for tracing.
I can inspect variable values with printf.
I can design test cases for good and bad input.
I can fix one bug at a time and retest.
I can explain the cause of the bug in plain English.
```

Chapter 30 should change how he studies every later chapter.

The standard is no longer:

```text
Did the command work once?
```

The standard is:

```text
Can I explain why it worked, how it can fail, and how I verified the fix?
```
