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

# Day 2 — Arithmetic Evaluation and Numbers

## Read before doing exercises

Read these Chapter 34 sections:

```text
Arithmetic Evaluation and Expansion
Number Bases
Unary Operators
Simple Arithmetic
Assignment
Bit Operations
Logic
The Comma Operator
```

## What he should gain from these sections

He should gain this skill:

```text
I can do integer arithmetic in Bash, understand what Bash can and cannot calculate, and avoid common number-input traps.
```

This matters because scripts often need to count, compare, loop, set thresholds, or calculate simple totals.

Feynman analogy:

```text
Bash arithmetic is like a small integer calculator built into the shell.
It is useful for counters and thresholds.
It is not a full scientific calculator.
```

---

## After reading: concept questions

Answer before typing:

1. What is arithmetic expansion?
2. What does `$(( expression ))` produce?
3. What is the difference between `$(( ))` and `(( ))`?
4. Does Bash arithmetic handle decimals naturally?
5. Why can leading zeroes be dangerous in Bash arithmetic?
6. What are number bases?
7. What does assignment inside arithmetic evaluation do?
8. What are unary operators such as `++` and `--` useful for?
9. Which arithmetic operators are useful immediately?
10. Which operators are recognition-level for now?

---

## Day 2 Exercise 1 — Arithmetic expansion

### Read again before the exercise

Reread:

```text
Arithmetic Evaluation and Expansion
Simple Arithmetic
```

### Skill being gained

He is learning when Bash treats text as an arithmetic expression.

### Do not type yet: predict

Predict each result:

```text
2 + 3 * 4
(2 + 3) * 4
10 / 3
10 % 3
```

### Run

```bash
printf '2 + 3 * 4 = %s\n' "$((2 + 3 * 4))"
printf '(2 + 3) * 4 = %s\n' "$(((2 + 3) * 4))"
printf '10 / 3 = %s\n' "$((10 / 3))"
printf '10 %% 3 = %s\n' "$((10 % 3))"
```

### Explain-back

Answer:

1. Why did `10 / 3` not produce `3.333...`?
2. What does `%` calculate?
3. Why is integer arithmetic still useful in scripts?
4. What kind of calculation should not be done with Bash integer arithmetic alone?

---

## Day 2 Exercise 2 — `(( ))` as a test-like command

### Read again before the exercise

Reread:

```text
Arithmetic Evaluation and Expansion
Logic
```

### Skill being gained

He is learning that arithmetic evaluation can also produce success/failure for branching.

### Do not type yet: predict

In Bash arithmetic:

```text
zero means false/failure
nonzero means true/success
```

Predict which branch runs.

### Create a script

```bash
cat > arithmetic-branch.sh <<'EOF'
#!/usr/bin/env bash

count=${1:-0}

if (( count > 10 )); then
    echo "count is greater than 10"
else
    echo "count is 10 or less"
fi
EOF

bash arithmetic-branch.sh 15
bash arithmetic-branch.sh 10
bash arithmetic-branch.sh 0
```

### Explain-back

Answer:

1. Why did `15` choose the first branch?
2. Why did `10` choose the second branch?
3. What does `count=${1:-0}` do?
4. What would happen if the user typed a non-number?

---

## Day 2 Exercise 3 — Counters and assignment

### Read again before the exercise

Reread:

```text
Unary Operators
Assignment
```

### Skill being gained

He is learning to change numeric variables deliberately.

### Do not type yet: predict

Predict the final count:

```text
start at 0
add 1
add 1
add 5
subtract 2
```

### Run

```bash
count=0
printf 'count=%s\n' "$count"

((count++))
printf 'after count++: %s\n' "$count"

((count++))
printf 'after count++: %s\n' "$count"

((count += 5))
printf 'after count += 5: %s\n' "$count"

((count -= 2))
printf 'after count -= 2: %s\n' "$count"
```

### Explain-back

Answer:

1. What did `count++` do?
2. What did `count += 5` do?
3. Why might explicit `count=$((count + 1))` be easier for a beginner to read?
4. When is compact arithmetic syntax worth using?

---

## Day 2 Exercise 4 — Number bases and the leading-zero trap

### Read again before the exercise

Reread:

```text
Number Bases
```

### Skill being gained

He is learning that numbers in Bash can be interpreted in different bases.

### Do not type yet: predict

Predict:

```text
2#1010 = ?
16#ff = ?
08 might be dangerous because ?
```

### Run

```bash
printf 'binary 1010 = %s\n' "$((2#1010))"
printf 'hex ff = %s\n' "$((16#ff))"
```

Now test user-style input safely:

```bash
num="08"
printf 'force base 10: %s\n' "$((10#$num))"
```

### Explain-back

Answer:

1. What does `2#1010` mean?
2. What does `16#ff` mean?
3. Why can leading zeroes be dangerous in numeric user input?
4. Why might `10#$num` be useful when reading numbers from users or files?

---

## Day 2 Exercise 5 — Recognition-level operators

### Read again before the exercise

Reread:

```text
Bit Operations
Logic
The Comma Operator
```

### Skill being gained

He is learning not to over-invest in rare features during the first pass.

### First-pass rule

For infrastructure scripting, these are usually **recognition-level** at first:

```text
bit shifts
bitwise AND / OR / XOR
comma operator
very compact arithmetic tricks
```

He should know they exist, but should not write clever scripts with them yet.

### Do not type yet: classify

Classify each as:

```text
must use soon
recognize for later
avoid until needed
```

```text
count++
count += 1
10#$num
2#1010
x << 1
x & 4
expr1, expr2
```

### Explain-back

Answer:

1. Why are clever operators dangerous for beginners?
2. What is more important: shorter code or explainable code?
3. When might bit operations be useful later?
4. Why should a script be boring and clear?

---

## Day 2 final mini-lab — Threshold checker

### Task

Create `threshold-check.sh`:

```bash
cat > threshold-check.sh <<'EOF'
#!/usr/bin/env bash

value=${1:?usage: threshold-check.sh VALUE LIMIT}
limit=${2:?usage: threshold-check.sh VALUE LIMIT}

value=$((10#$value))
limit=$((10#$limit))

if (( value >= limit )); then
    printf 'WARN: value %d is greater than or equal to limit %d\n' "$value" "$limit"
else
    printf 'OK: value %d is below limit %d\n' "$value" "$limit"
fi
EOF
```

Test:

```bash
bash threshold-check.sh 9 10
bash threshold-check.sh 10 10
bash threshold-check.sh 12 10
bash threshold-check.sh 08 10
bash threshold-check.sh
```

### Explain-back

Answer:

1. What is the script's command-line contract?
2. Why does it use `${1:?}` and `${2:?}`?
3. Why does it use `10#`?
4. Which branch should run for each test case?
5. What input is still not handled well?

---

## Day 2 finish standard

He is done with Day 2 only if he can explain:

```text
$(( expression ))
(( expression ))
integer division
remainder with %
count++
count += 1
number bases such as 2#1010 and 16#ff
why 10#$num can be useful
why Bash is not enough for decimal math
```

---
