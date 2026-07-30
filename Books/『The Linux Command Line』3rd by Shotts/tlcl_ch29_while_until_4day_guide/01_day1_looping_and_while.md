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
# Day 1: Looping and `while`

## Read before exercises

Read from the start of Chapter 29 through the section:

```text
while
```

Read slowly. The important idea is not syntax. The important idea is that Bash repeats commands while a test succeeds.

## What he should gain

He should gain this mental model:

```text
A while loop repeats while a command returns success.
In Bash, success means exit status 0.
Failure means non-zero.
```

That is different from some programming languages where `0` means false.

---

# Before reading: Feynman preview

Explain this before reading:

```text
An if statement asks a question once.
A while loop keeps asking the same question before each repetition.
```

Example in plain English:

```text
While the answer is not q, show the menu again.
```

The loop needs three parts:

```text
starting state
condition
something inside the loop that can change the condition
```

---

# After reading: concept questions

Answer without looking back:

1. What does `while` test?
2. What does Bash mean by command success?
3. What exit status usually means success?
4. Why can a `while` loop become infinite?
5. What must change inside or around the loop?
6. How is `while` different from `if`?
7. Why should you write the condition in English before typing the loop?

Do not continue until these are answered.

---

# Exercise 1: A counter loop

## Skill being gained

He is learning the anatomy of a loop:

```text
initialize
→ test
→ body
→ update
→ stop
```

## Do not type yet

Predict the output:

```text
What number prints first?
What number prints last?
What makes the loop stop?
What would happen if number never changed?
```

## Type and run

```bash
cd ~/tlcl-ch29-loops

cat > count-while.sh <<'EOF'
#!/usr/bin/env bash

number=1

while [[ "$number" -le 5 ]]; do
    echo "number=$number"
    number=$((number + 1))
done
EOF

bash count-while.sh
```

## Inspect

Answer:

1. Was your prediction correct?
2. What was the first value of `number`?
3. What was the last printed value?
4. What was the value that made the condition fail?
5. Which line prevents an infinite loop?

---

# Exercise 2: Break it deliberately

## Skill being gained

He is learning that infinite loops usually happen because the condition never changes.

## Do not type yet

Predict what will happen if the update line is removed.

## Create a safe broken version

Use a safety counter so it cannot run forever:

```bash
cat > broken-but-safe.sh <<'EOF'
#!/usr/bin/env bash

number=1
safety=1

while [[ "$number" -le 5 ]]; do
    echo "number=$number safety=$safety"
    safety=$((safety + 1))

    if [[ "$safety" -gt 8 ]]; then
        echo "Safety stop: the real loop condition is not changing."
        break
    fi
done
EOF

bash broken-but-safe.sh
```

## Explain-back

Explain:

```text
The loop condition depends on number.
But number never changes.
So the loop would continue forever without the safety break.
```

---

# Exercise 3: Build the first repeating menu

## Skill being gained

He is learning how Chapter 28 input combines with Chapter 29 loops.

## Read before exercise

Reread the first `while` example in Chapter 29.

Ask:

```text
What variable controls the loop?
What user input changes that variable?
```

## Do not type yet

Write answers:

```text
Loop condition in English:
Variable controlling the loop:
Input that stops the loop:
What happens for unknown input:
```

## Create script

```bash
cat > lab-menu.sh <<'EOF'
#!/usr/bin/env bash

choice=""

while [[ "$choice" != "q" ]]; do
    echo
    echo "Lab Menu"
    echo "1. Show date"
    echo "2. Show hostname"
    echo "q. Quit"
    read -r -p "Choice: " choice

    if [[ "$choice" == "1" ]]; then
        date
    elif [[ "$choice" == "2" ]]; then
        hostname
    elif [[ "$choice" == "q" ]]; then
        echo "Goodbye."
    else
        echo "Unknown choice: $choice"
    fi
done
EOF

bash lab-menu.sh
```

Test these inputs:

```text
1
2
x
empty Enter
q
```

## Explain-back

Answer:

1. What value does `choice` have before the loop starts?
2. Why is it initialized?
3. Which input stops the loop?
4. What happens after invalid input?
5. Why does the menu reappear?
6. Why is `read -r` used instead of plain `read`?

---

# Day 1 finish standard

He is done only if he can say:

```text
A while loop repeats while its condition succeeds.
The loop must contain or depend on something that can eventually change the condition.
I can identify the variable, the condition, the body, and the stopping case.
```
