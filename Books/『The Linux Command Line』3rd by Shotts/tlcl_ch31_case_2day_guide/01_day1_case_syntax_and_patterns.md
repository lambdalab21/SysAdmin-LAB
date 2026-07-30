# Day 1: `case` Syntax and Pattern Thinking

## Read before exercises

Read Chapter 31 from the beginning through these ideas:

```text
case
Patterns
```

If your edition says only **The case Command**, read the part that introduces the command syntax and the examples showing different choices.

## What he should gain from this reading

He should gain this idea:

```text
`case` is for choosing one branch from several pattern-based choices.
```

He is not learning a new magic command. He is learning a clearer way to express a many-choice decision.

---

# Before reading: Feynman preview

Explain this before reading:

```text
Suppose a script asks:
1) system info
2) disk space
3) home usage
q) quit

Using many if/elif tests works, but it becomes visually noisy.
A case statement lets each choice have its own labeled branch.
```

Feynman version:

```text
`case` is a sorting desk.
The input arrives.
The script compares it against labels.
The first matching label gets the job.
If no label matches, the default label handles it.
```

---

# After reading: concept questions

Answer without looking back:

1. What problem does `case` solve better than a long `if` / `elif` chain?
2. What does `case "$choice" in` mean?
3. Why does each pattern end with `)`?
4. What does `;;` do?
5. What does `esac` mean structurally?
6. What does the `*` pattern usually mean?
7. Does `case` use normal regular expressions like `grep -E`?
8. If more than one pattern could match, which branch runs?
9. Why should the variable usually be quoted in `case "$choice" in`?
10. What invalid input should your script test?

Do not do the exercises until these are answered.

---

# Setup

Create a safe working directory:

```bash
mkdir -p ~/tlcl-ch31-case
cd ~/tlcl-ch31-case
```

---

# Exercise 1: Build the smallest `case` statement

## Skill being gained

He is learning the basic skeleton:

```bash
case "$variable" in
    pattern)
        commands
        ;;
    *)
        default commands
        ;;
esac
```

## Do not type yet

Before typing, answer:

```text
What input will I read?
What three values will I accept?
What should happen for bad input?
Which branch should run for each test?
```

## Create the script

```bash
cat > action-case.sh <<'EOF'
#!/usr/bin/env bash

read -r -p "Action? start, stop, status: " action

case "$action" in
    start)
        echo "Starting service..."
        ;;
    stop)
        echo "Stopping service..."
        ;;
    status)
        echo "Showing service status..."
        ;;
    *)
        echo "Unknown action: $action"
        ;;
esac
EOF
```

Run syntax check first:

```bash
bash -n action-case.sh
```

Run all important cases:

```bash
bash action-case.sh
```

Test these inputs one by one:

```text
start
stop
status
restart
empty input
START
```

## Explain-back

Fill this in:

```text
Input: start
Expected branch:
Actual branch:
Why:

Input: restart
Expected branch:
Actual branch:
Why:
```

Then explain each keyword:

```text
case:
in:
pattern):
;;:
*):
esac:
```

---

# Exercise 2: Understand pattern matching

## Skill being gained

He is learning that `case` patterns are shell-style patterns, not `grep` regex.

## Read before exercise

Reread the part about `Patterns`.

Also recall from TLCL Ch. 7:

```text
*        any string
?        any single character
[...]    one character from a set
```

## Do not type yet

Predict which branch each filename should match:

```text
notes.txt
app.log
backup.tar.gz
README
9-data.csv
.hidden
```

## Create the script

```bash
cat > file-type-case.sh <<'EOF'
#!/usr/bin/env bash

read -r -p "Filename: " filename

case "$filename" in
    *.txt)
        echo "Text file"
        ;;
    *.log)
        echo "Log file"
        ;;
    *.tar.gz)
        echo "Compressed tar archive"
        ;;
    [0-9]*)
        echo "Starts with a digit"
        ;;
    .*)
        echo "Hidden dot file"
        ;;
    *)
        echo "Other file"
        ;;
esac
EOF

bash -n file-type-case.sh
```

Run it with the predicted filenames.

## Explain-back

Answer:

1. Why does `*.txt` match `notes.txt`?
2. Why does `[0-9]*` match `9-data.csv`?
3. Is `[0-9]*` a regex or a shell pattern here?
4. What does the final `*)` branch do?
5. Why is it useful to have a default branch?

---

# Exercise 3: First matching pattern wins

## Skill being gained

He is learning that branch order matters.

## Do not type yet

Predict the output for `error.log`:

```text
Will it match *.log or error.*?
Which appears first?
```

## Create the script

```bash
cat > first-match.sh <<'EOF'
#!/usr/bin/env bash

name="error.log"

case "$name" in
    *.log)
        echo "Matched log file"
        ;;
    error.*)
        echo "Matched error file"
        ;;
    *)
        echo "No match"
        ;;
esac
EOF

bash first-match.sh
```

Now reverse the two branches and run again.

## Explain-back

Answer:

1. Did both patterns match logically?
2. Which branch actually ran?
3. Why does order matter?
4. When should a more specific pattern come before a more general pattern?

---

# Exercise 4: Compare `if` and `case`

## Skill being gained

He is learning when `case` is clearer than `if`.

## Read after exercise

After this exercise, reread the first example in Chapter 31 and ask:

```text
Could this have been written with if?
Why is case easier to read?
```

## Create the `if` version mentally

Do not type the whole thing yet. Sketch this in plain English:

```text
if action is start, do start
elif action is stop, do stop
elif action is status, do status
else unknown
```

Now compare that to the `case` version.

## Explain-back

Answer:

```text
`if` is better when:
`case` is better when:
```

A good answer:

```text
`if` is better when I am testing different conditions.
`case` is better when one input is being compared against several possible patterns.
```

---

# Day 1 finish standard

He is done with Day 1 only if he can explain:

```text
A case statement compares one value against patterns.
Each branch has a pattern, commands, and ;;.
The first matching branch runs.
The *) branch is the default.
The patterns are shell patterns, not grep regular expressions.
Branch order matters.
```
