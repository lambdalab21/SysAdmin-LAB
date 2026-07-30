# TLCL Chapter 29: Flow Control — Looping with `while` / `until`

This guide is for Chapter 29 of William Shotts's *The Linux Command Line*.

The purpose is not to memorize loop syntax.

The purpose is to learn disciplined control-flow thinking:

```text
What condition controls the loop?
What changes each pass?
When does the loop stop?
What happens if the input is empty, wrong, or unexpected?
Can I explain why the loop ran this many times?
```

Feynman analogy:

```text
A loop is like a parent saying:
"Keep doing this chore while the room is still messy."

The key is not the word "while."
The key is the stopping condition.
If nobody changes the room, the chore never ends.
```

Working directory for all days:

```bash
mkdir -p ~/tlcl-ch29-loops
cd ~/tlcl-ch29-loops
```

Continuing project:

```text
lab-menu.sh
```

This script will grow from a simple repeating menu into a safer tool that can loop, quit, reject bad input, and read a file line by line.

---
# Day 3: `until` and Condition Design

## Read before exercises

Read the Chapter 29 section:

```text
until
```

## What he should gain

He should gain this idea:

```text
while repeats while a condition succeeds.
until repeats until a condition succeeds.
```

The same logic can often be written either way. The disciplined thinker chooses the version that reads most clearly in English.

---

# Before reading: Feynman preview

Compare these sentences:

```text
While the server is not ready, keep waiting.
Until the server is ready, keep waiting.
```

They describe the same behavior.

The question is:

```text
Which one is easier to read without mental gymnastics?
```

---

# After reading: concept questions

Answer without looking back:

1. What does `until` test?
2. When does an `until` loop stop?
3. How is `until` different from `while`?
4. Can every `until` loop be rewritten as a `while` loop?
5. Why might `until` sometimes read more naturally?
6. What danger does `until` share with `while`?

---

# Exercise 1: Rewrite `while` as `until`

## Skill being gained

He is learning that loop meaning comes from the condition, not from memorized syntax.

## Do not type yet

Predict the output:

```text
What number prints first?
What number prints last?
When does the loop stop?
```

## Create script

```bash
cd ~/tlcl-ch29-loops

cat > count-until.sh <<'EOF'
#!/usr/bin/env bash

number=1

until [[ "$number" -gt 5 ]]; do
    echo "number=$number"
    number=$((number + 1))
done
EOF

bash count-until.sh
```

## Compare with Day 1

Run:

```bash
bash count-while.sh
bash count-until.sh
```

Answer:

1. Do the scripts produce the same output?
2. Which condition is easier to explain?
3. In the `while` version, what condition keeps the loop going?
4. In the `until` version, what condition stops the loop?

---

# Exercise 2: Waiting loop with a safety limit

## Skill being gained

He is learning how to design loops that wait without becoming uncontrolled.

## Do not type yet

Answer:

```text
What condition are we waiting for?
What could go wrong?
How can we stop after too many tries?
```

## Create script

```bash
cat > wait-for-file.sh <<'EOF'
#!/usr/bin/env bash

file="ready.txt"
tries=0
max_tries=5

until [[ -f "$file" ]]; do
    tries=$((tries + 1))
    echo "Waiting for $file... try $tries of $max_tries"

    if [[ "$tries" -ge "$max_tries" ]]; then
        echo "Giving up. $file was not created."
        exit 1
    fi

    sleep 1
done

echo "$file exists. Continuing."
EOF

bash wait-for-file.sh
```

Now create the file and test again:

```bash
touch ready.txt
bash wait-for-file.sh
rm -f ready.txt
```

## Explain-back

Answer:

1. What does `[[ -f "$file" ]]` test?
2. Why is there a maximum number of tries?
3. Why is `$file` quoted?
4. Why does the script use `exit 1` when it gives up?
5. What would make this loop dangerous without `max_tries`?

---

# Exercise 3: Choose `while` or `until`

## Skill being gained

He is learning to choose the loop that expresses the idea clearly.

For each English rule, choose `while` or `until` and explain why.

1. Keep asking until the user enters a non-empty name.
2. While the counter is less than 10, keep counting.
3. Until the file exists, keep waiting.
4. While the user has not chosen quit, keep showing the menu.
5. Until the command succeeds, try again.

Write one Bash condition for each.

Example:

```text
Rule: Until the file exists, keep waiting.
Choice: until
Condition: [[ -f "$file" ]]
Reason: the success condition is the stopping condition.
```

---

# Day 3 finish standard

He is done only if he can say:

```text
while is natural when I describe the continuing condition.
until is natural when I describe the stopping success condition.
Both can loop forever if nothing changes.
I can add a safety limit to waiting loops.
```
