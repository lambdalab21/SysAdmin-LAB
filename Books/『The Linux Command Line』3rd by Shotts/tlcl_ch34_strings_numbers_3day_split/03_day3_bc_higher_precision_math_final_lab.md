# TLCL Chapter 34 — Strings and Numbers


Book: William Shotts, *The Linux Command Line*, Chapter 34, **“Strings and Numbers.”**

This chapter should be a **three-day work** because it contains many small Bash features that are easy to type mechanically and forget.

The goal is not to memorize every form of parameter expansion.

The goal is this:

```text
When a script receives text or a number, I can explain:
1. what value exists,
2. how Bash expands it,
3. whether it is a string or an integer,
4. what transformation I am applying,
5. how I verified the result.
```

Feynman rule:

```text
Do not say “Bash magic.”
Explain it as if teaching a younger student:
“This expression takes this variable, applies this rule, and produces this text.”
```

Disciplined thinking rule:

```text
Before typing:
- What value do I start with?
- What exact output do I expect?
- What happens if the variable is empty?
- What happens if the variable is unset?
- What happens if the number has strange input?

After running:
- Did Bash produce what I predicted?
- If not, which assumption was wrong?
```

---

## 3-day split

| Day | Read before exercises | Main gain |
|---|---|---|
| Day 1 | `Parameter Expansion`, `Basic Parameters`, `Expansions to Manage Empty Variables`, `Expansions That Return Variable Names`, `String Operations`, `Case Conversion` | Treat strings as data that Bash can inspect and transform |
| Day 2 | `Arithmetic Evaluation and Expansion`, `Number Bases`, `Unary Operators`, `Simple Arithmetic`, `Assignment`, `Bit Operations`, `Logic`, `The Comma Operator` | Treat numbers carefully; understand Bash integer arithmetic and operator behavior |
| Day 3 | `bc—An Arbitrary Precision Calculator Language`, `Using bc`, `An Example Script`, `Summing Up`, optional `Extra Credit` | Know when Bash arithmetic is not enough and build a small script using strings and numbers |

---

# Setup for all three days

Create a safe working directory:

```bash
mkdir -p ~/tlcl-ch34-strings-numbers
cd ~/tlcl-ch34-strings-numbers
```

Create a scratch script file when needed:

```bash
: > scratch.sh
```

Safety rule:

```text
Do all experiments in ~/tlcl-ch34-strings-numbers.
Do not run these examples in important project directories.
```

---

# Day 3 — `bc`, Higher-Precision Math, and Final Lab

## Read before doing exercises

Read these Chapter 34 sections:

```text
bc—An Arbitrary Precision Calculator Language
Using bc
An Example Script
Summing Up
```

Optional after the main exercises:

```text
Extra Credit
```

## What he should gain from these sections

He should gain this skill:

```text
I know when Bash integer arithmetic is enough and when I need a tool such as bc for decimal or higher-precision arithmetic.
```

Feynman analogy:

```text
Bash arithmetic is a pocket counter for whole numbers.
bc is a calculator for more serious arithmetic.
```

---

## After reading: concept questions

Answer before typing:

1. What kind of arithmetic can Bash do easily?
2. What kind of arithmetic is `bc` better for?
3. Why does decimal math matter in real scripts?
4. How can a script send expressions to `bc`?
5. Why should input still be validated before giving it to a calculator tool?
6. What is the difference between calculating and formatting output?
7. Why should a script check whether required commands exist?

---

## Day 3 Exercise 1 — Check whether `bc` exists

### Skill being gained

He is learning to check dependencies instead of assuming tools exist.

### Do not type yet: predict

Answer:

```text
What should the script do if bc is missing?
Should it fail silently or explain the problem?
```

### Run

```bash
command -v bc
```

Create a dependency check:

```bash
cat > bc-check.sh <<'EOF'
#!/usr/bin/env bash

if ! command -v bc >/dev/null 2>&1; then
    echo "Error: bc is required but not installed." >&2
    exit 1
fi

echo "bc is available."
EOF

bash bc-check.sh
```

### Explain-back

Answer:

1. What does `command -v bc` check?
2. Why redirect output to `/dev/null`?
3. Why send the error message to stderr with `>&2`?
4. Why exit with a nonzero status when a dependency is missing?

---

## Day 3 Exercise 2 — Compare Bash integer arithmetic with `bc`

### Read again before the exercise

Reread:

```text
Using bc
```

### Skill being gained

He is learning the practical boundary between Bash and `bc`.

### Do not type yet: predict

Predict:

```text
Bash 10 / 3 = ?
bc 10 / 3 with scale=2 = ?
```

### Run

```bash
printf 'Bash integer division: %s\n' "$((10 / 3))"
printf 'scale=2; 10 / 3\n' | bc
printf 'scale=4; 10 / 3\n' | bc
```

### Explain-back

Answer:

1. Why did Bash and `bc` differ?
2. What does `scale` control?
3. Why might decimal results matter in a script?
4. Why is Bash still better for simple counters?

---

## Day 3 Exercise 3 — Use a here document with `bc`

### Skill being gained

He is learning to feed a multi-line calculation to another program clearly.

### Do not type yet: predict

Before running, identify:

```text
Which part is the shell script?
Which part is bc input?
Which variables are expanded by Bash before bc sees them?
```

### Create a script

```bash
cat > percent.sh <<'EOF'
#!/usr/bin/env bash

part=${1:?usage: percent.sh PART WHOLE}
whole=${2:?usage: percent.sh PART WHOLE}

if ! command -v bc >/dev/null 2>&1; then
    echo "Error: bc is required." >&2
    exit 1
fi

bc <<BC_INPUT
scale=2
($part / $whole) * 100
BC_INPUT
EOF

bash percent.sh 3 10
bash percent.sh 1 3
```

### Explain-back

Answer:

1. Which lines are interpreted by Bash?
2. Which lines are sent to `bc`?
3. What danger exists if `part` or `whole` contain strange input?
4. How could validation improve this script?

---

## Day 3 Exercise 4 — Add basic input validation

### Skill being gained

He is learning that calculation scripts must not trust input blindly.

### Do not type yet: design the validation

Write rules in English:

```text
part must be digits only.
whole must be digits only.
whole must not be zero.
```

### Create a safer version

```bash
cat > percent-safe.sh <<'EOF'
#!/usr/bin/env bash

part=${1:?usage: percent-safe.sh PART WHOLE}
whole=${2:?usage: percent-safe.sh PART WHOLE}

if [[ ! "$part" =~ ^[0-9]+$ ]]; then
    echo "Error: PART must be digits only." >&2
    exit 1
fi

if [[ ! "$whole" =~ ^[0-9]+$ ]]; then
    echo "Error: WHOLE must be digits only." >&2
    exit 1
fi

if (( 10#$whole == 0 )); then
    echo "Error: WHOLE must not be zero." >&2
    exit 1
fi

if ! command -v bc >/dev/null 2>&1; then
    echo "Error: bc is required." >&2
    exit 1
fi

bc <<BC_INPUT
scale=2
($part / $whole) * 100
BC_INPUT
EOF
```

Test both good and bad cases:

```bash
bash percent-safe.sh 3 10
bash percent-safe.sh 1 3
bash percent-safe.sh x 10
bash percent-safe.sh 3 y
bash percent-safe.sh 3 0
```

### Explain-back

Answer:

1. Why use regex validation before calculation?
2. Why use `10#$whole` in the zero check?
3. Why is division by zero checked before calling `bc`?
4. Which test cases should succeed?
5. Which test cases should fail?
6. Are decimal inputs allowed? Why or why not?

---

## Day 3 final lab — Script using strings and numbers

### Goal

Build a small script that uses:

```text
parameter expansion
string normalization
integer comparison
optional bc decimal calculation
clear error messages
```

### Task: `usage-percent.sh`

This script receives two numbers and one label:

```text
usage-percent.sh USED TOTAL LABEL
```

Example:

```bash
bash usage-percent.sh 37 100 "App01 Disk"
```

Expected behavior:

```text
Label is normalized for display.
USED and TOTAL are validated.
TOTAL cannot be zero.
The script prints USED / TOTAL as a percentage.
If the percentage is 80 or more, it prints WARN.
Otherwise it prints OK.
```

### Do not type yet: design

Fill this in before writing the script:

```text
Inputs:
Required values:
Validation rules:
String transformation:
Integer comparison:
Decimal calculation:
Success cases:
Failure cases:
```

### Create the script

```bash
cat > usage-percent.sh <<'EOF'
#!/usr/bin/env bash

used=${1:?usage: usage-percent.sh USED TOTAL LABEL}
total=${2:?usage: usage-percent.sh USED TOTAL LABEL}
label=${3:?usage: usage-percent.sh USED TOTAL LABEL}

label=${label,,}
label=${label// /-}

if [[ ! "$used" =~ ^[0-9]+$ ]]; then
    echo "Error: USED must be digits only." >&2
    exit 1
fi

if [[ ! "$total" =~ ^[0-9]+$ ]]; then
    echo "Error: TOTAL must be digits only." >&2
    exit 1
fi

used_int=$((10#$used))
total_int=$((10#$total))

if (( total_int == 0 )); then
    echo "Error: TOTAL must not be zero." >&2
    exit 1
fi

if ! command -v bc >/dev/null 2>&1; then
    echo "Error: bc is required." >&2
    exit 1
fi

percent=$(bc <<BC_INPUT
scale=2
($used_int / $total_int) * 100
BC_INPUT
)

# Integer threshold check. This is intentionally simple.
if (( used_int * 100 >= total_int * 80 )); then
    status="WARN"
else
    status="OK"
fi

printf 'label=%s\n' "$label"
printf 'used=%d\n' "$used_int"
printf 'total=%d\n' "$total_int"
printf 'percent=%s%%\n' "$percent"
printf 'status=%s\n' "$status"
EOF
```

Test:

```bash
bash usage-percent.sh 37 100 "App01 Disk"
bash usage-percent.sh 80 100 "App01 Disk"
bash usage-percent.sh 08 10 "DB01 Backup"
bash usage-percent.sh x 10 "Bad Input"
bash usage-percent.sh 3 0 "Zero Test"
```

### Explain-back

Answer:

1. Which line normalizes the label to lowercase?
2. Which line replaces spaces with hyphens?
3. Which lines validate numeric input?
4. Which lines force base-10 interpretation?
5. Which line prevents division by zero?
6. Which part uses `bc`?
7. Which part uses Bash integer arithmetic instead of `bc`?
8. Why is the threshold comparison written as `used_int * 100 >= total_int * 80`?
9. What would be required to support decimal input?
10. What would you change before using this script in a real admin task?

---

## Read after final lab

Reread:

```text
Summing Up
```

Optional:

```text
Extra Credit
```

Read with this question:

```text
Which features from Chapter 34 are essential for writing clear scripts, and which are advanced features I only need to recognize for now?
```

---

# Final Chapter 34 self-test

Answer without looking at notes.

## Parameter expansion

1. What problem does `${var}` solve compared with `$var`?
2. What does `${var:-word}` do?
3. What does `${var:=word}` do?
4. What does `${var:?message}` do?
5. What does `${#var}` return?
6. What does `${var##*/}` commonly extract from a path?
7. What does `${var%.*}` commonly do to a filename?
8. What does `${var//old/new}` do?
9. What does `${var,,}` do?
10. Why should expansions usually be quoted?

## Arithmetic

1. What does `$(( ))` do?
2. What does `(( ))` do in an `if` statement?
3. Why is `10 / 3` equal to `3` in Bash arithmetic?
4. What does `%` do?
5. Why can `08` be a problem?
6. What does `10#$num` mean?
7. What is `count++`?
8. Why should beginners avoid overly clever arithmetic expressions?

## `bc`

1. Why use `bc`?
2. What does `scale` control?
3. How can a script send multiple lines to `bc`?
4. Why should a script validate input before calculation?
5. What should a script do if `bc` is not installed?

---

# Final Feynman explanation

Explain Chapter 34 in plain English:

```text
This chapter teaches that scripts do not only process files.
They also process smaller pieces of data: strings and numbers.
Bash can inspect and transform strings using parameter expansion.
Bash can do integer arithmetic for counters and thresholds.
For decimal or higher-precision math, a script can use bc.
The disciplined habit is to know what value exists, what expansion or calculation will happen, and how to test normal and abnormal cases.
```

Then explain these five examples:

```bash
file="/var/log/nginx/access.log"
echo "${file##*/}"
echo "${file%.*}"
count=$((count + 1))
if (( used * 100 >= total * 80 )); then echo WARN; fi
printf 'scale=2; 1 / 3\n' | bc
```

If he cannot explain one line, he should go back to the relevant day.

---

# Chapter 34 completion standard

He is ready to move on only if he can:

```text
- use parameter expansion without guessing,
- explain unset vs empty variables,
- transform strings safely,
- use Bash integer arithmetic for counters and thresholds,
- avoid the leading-zero number trap,
- know when to use bc,
- validate inputs before calculation,
- test both normal and abnormal cases,
- explain every line of the final script.
```
