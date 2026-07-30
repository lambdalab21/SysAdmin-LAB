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
# Day 2: `break`, `continue`, `select`, and Menu Loops

## Read before exercises

Read the Chapter 29 section:

```text
break and continue
```

If your edition includes the section:

```text
select
```

read it after completing Exercise 2. Treat `select` as useful recognition knowledge, not as the main scripting style yet.

## What he should gain

He should gain this idea:

```text
A loop normally stops because its condition fails.
`break` stops the loop immediately.
`continue` skips the rest of this pass and starts the next pass.
```

He should not use these commands randomly. He should explain why normal loop logic is not enough.

---

# Before reading: Feynman preview

A loop is like a checklist repeated every day.

```text
continue = skip today's remaining steps and start tomorrow
break    = stop the repeated checklist entirely
```

Disciplined question:

```text
Am I ending the loop, or just skipping this one pass?
```

---

# After reading: concept questions

Answer without looking back:

1. What does `break` do?
2. What does `continue` do?
3. How are they different?
4. Why can overusing them make scripts harder to read?
5. What is a good reason to use `break`?
6. What is a good reason to use `continue`?
7. What should you test after adding either one?

---

# Exercise 1: Add `break` to a menu

## Skill being gained

He is learning to stop a loop from inside the body.

## Do not type yet

Predict:

```text
What happens when choice is q?
Does the condition get checked again after break?
What message prints before the loop exits?
```

## Create script

```bash
cd ~/tlcl-ch29-loops

cat > menu-break.sh <<'EOF'
#!/usr/bin/env bash

while true; do
    echo
    echo "Lab Menu"
    echo "1. Show date"
    echo "2. Show hostname"
    echo "q. Quit"
    read -r -p "Choice: " choice

    if [[ "$choice" == "q" ]]; then
        echo "Goodbye."
        break
    elif [[ "$choice" == "1" ]]; then
        date
    elif [[ "$choice" == "2" ]]; then
        hostname
    else
        echo "Unknown choice: $choice"
    fi
done

 echo "Menu finished."
EOF

bash -n menu-break.sh
bash menu-break.sh
```

## Inspect

Test:

```text
1
x
q
```

Answer:

1. Why does `while true` run forever by itself?
2. Which line escapes the loop?
3. What would happen if `break` were removed?
4. Is this clearer or less clear than `while [[ "$choice" != "q" ]]`? Why?

---

# Exercise 2: Use `continue` to reject bad input early

## Skill being gained

He is learning to skip the rest of a loop pass when input is invalid.

## Do not type yet

Predict:

```text
What should happen for an empty answer?
What should happen for q?
What should happen for 1?
```

## Create script

```bash
cat > menu-continue.sh <<'EOF'
#!/usr/bin/env bash

while true; do
    echo
    echo "Lab Menu"
    echo "1. Show date"
    echo "2. Show hostname"
    echo "q. Quit"
    read -r -p "Choice: " choice

    if [[ -z "$choice" ]]; then
        echo "Please enter a choice."
        continue
    fi

    if [[ "$choice" == "q" ]]; then
        echo "Goodbye."
        break
    fi

    if [[ "$choice" == "1" ]]; then
        date
    elif [[ "$choice" == "2" ]]; then
        hostname
    else
        echo "Unknown choice: $choice"
    fi
done
EOF

bash menu-continue.sh
```

## Explain-back

Answer:

1. What does `-z "$choice"` test?
2. What lines are skipped after `continue`?
3. Why is this cleaner than deeply nested `if` statements?
4. What inputs did you test?

---

# Exercise 3: `select` as recognition knowledge

## Skill being gained

He is learning to recognize Bash's built-in menu loop.

This is useful, but not the main tool for every script. Menus built manually with `read` are often clearer for learning.

## Read before exercise

Read the `select` section if your edition includes it.

## Do not type yet

Predict:

```text
What menu numbers will Bash generate?
Where will the chosen word be stored?
What happens for an invalid number?
```

## Try a small example

```bash
cat > select-demo.sh <<'EOF'
#!/usr/bin/env bash

PS3="Pick a command: "

select item in date hostname quit; do
    if [[ "$item" == "quit" ]]; then
        echo "Goodbye."
        break
    elif [[ "$item" == "date" ]]; then
        date
    elif [[ "$item" == "hostname" ]]; then
        hostname
    else
        echo "Invalid selection."
    fi
done
EOF

bash select-demo.sh
```

## Explain-back

Answer:

1. What does `select` create automatically?
2. What does `PS3` control?
3. Why might manual `read` menus still be better for learning?
4. When might `select` be useful?

---

# Day 2 finish standard

He is done only if he can say:

```text
break exits the loop.
continue skips to the next loop test/pass.
select can create simple menus, but I still need to understand the loop logic underneath.
I can test quit, bad input, empty input, and normal input.
```
