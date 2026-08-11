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
# Day 1: Traditional `for` Form over Simple Lists

## Read before exercises

Read from the start of Chapter 33 through the first part of:

```text
for: Traditional Shell Form
```

Focus on the basic form:

```bash
for variable in words; do
    commands
 done
```

or the multiline form shown in the book.

## What he should gain

He should gain this mental model:

```text
Bash takes a list of words.
On each pass, it assigns the next word to the loop variable.
Then it runs the loop body.
```

He is learning list-based thinking.

---

# After reading: concept questions

Answer before typing commands:

1. What does the loop variable contain during the first pass?
2. What does it contain during the second pass?
3. When does the loop stop?
4. Is the loop variable permanent or temporary?
5. What does the word `do` mark?
6. What does the word `done` mark?
7. Why is the list after `in` important?
8. What happens if the list has four words?
9. How is a `for` loop different from a `while` loop?
10. Explain a `for` loop to a 10-year-old without using the word “iteration.”

Do not continue until these are answered.

---

# Exercise 1: Predict a simple word list

## Read before this exercise

Reread the first example of the traditional shell form.

## Skill being gained

He is learning to trace a loop one pass at a time.

## Do not type yet

For this loop, predict every line of output:

```bash
for name in alpha beta gamma; do
    echo "name=$name"
done
```

Write:

```text
Pass 1: name = _____, output = _____
Pass 2: name = _____, output = _____
Pass 3: name = _____, output = _____
After pass 3: what happens?
```

## Run

```bash
for name in alpha beta gamma; do
    echo "name=$name"
done
```

## Inspect

Did the output match the prediction?

## Read after this exercise

Look again at the syntax of the `for` loop. Identify the list, variable, and body in the book’s example and in your example.

## Explain-back

Fill this in:

```text
List source:
Variable name:
Loop body:
Number of passes:
Reason the loop stopped:
```

---

# Exercise 2: Make the list meaningful

## Read before this exercise

Stay in **`for`: Traditional Shell Form**. Pay attention to the idea that the words after `in` are the list.

## Skill being gained

He is learning to name the loop variable after the kind of item being processed.

Bad:

```bash
for x in app01 db01 playground01; do ...
```

Better:

```bash
for host in app01 db01 playground01; do ...
```

## Do not type yet

Answer:

```text
What is the list?
What is one item?
What should the variable be called?
What repeated work should happen to each item?
```

## Run

```bash
for host in app01 db01 playground01; do
    echo "Would check host: $host"
done
```

## Inspect

Why did we say `Would check` instead of actually connecting with `ssh`?

## Read after this exercise

Reread the syntax and focus on how the loop body can contain any command, but discipline requires previewing before doing real work.

## Explain-back

Explain this in plain English:

```bash
for host in app01 db01 playground01; do
    echo "Would check host: $host"
done
```

Use this format:

```text
Bash creates a list containing ...
On the first pass, host is ...
On the second pass, host is ...
On the third pass, host is ...
Each time, Bash runs ...
```

---

# Exercise 3: Create a small script using a `for` loop

## Read before this exercise

Reread **`for`: Traditional Shell Form** and ask:

```text
Where does the list come from?
What work repeats?
What output should I see?
```

## Skill being gained

He is learning to put a loop into a script and test it safely.

## Create the script

```bash
cat > host-preview.sh <<'EOF'
#!/usr/bin/env bash

# host-preview.sh - preview work for a list of lab hosts

for host in app01 db01 playground01; do
    echo "Would collect basic report from: $host"
done
EOF
```

Run with Bash first:

```bash
bash host-preview.sh
```

Make executable only after it works:

```bash
chmod +x host-preview.sh
./host-preview.sh
```

## Read after this exercise

Reread the paragraph explaining the traditional shell form. Then locate the list, variable, and repeated command inside `host-preview.sh`.

## Explain-back

Answer:

1. Why did we run `bash host-preview.sh` before `chmod +x`?
2. What is the exact list?
3. What is one item from the list?
4. What command repeats?
5. What would be the danger of replacing `echo` with a real destructive command too early?

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
A traditional for loop repeats commands once for each word in a list.
The loop variable receives one item at a time.
The loop body is the work repeated for each item.
I can trace the loop pass by pass before running it.
```
