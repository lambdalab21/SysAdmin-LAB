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

---

# After reading: concept questions

Answer without looking back:

1. What problem does `case` solve better than a long `if` / `elif` chain? Clean multi-way choice on one value instead of long if/elif. 
2. What does `case "$choice" in` mean? Compare the value of $choice against the following pattern. 
3. Why does each pattern end with `)`? Marks the end of the pattern. 
4. What does `;;` do? Ends the branch. 
5. What does `esac` mean structurally? Closes the case block.
6. What does the `*` pattern usually mean? It usually defaults to 'anything else'.
7. Does `case` use normal regular expressions like `grep -E`? No. Shell glob patterns. 
8. If more than one pattern could match, which branch runs? The first matching one. 
9. Why should the variable usually be quoted in `case "$choice" in`? It prevents word-splitting and empty-value surprises. 
10. What invalid input should your script test? Empty, wrong case, unknown values. 

---

# Setup

Create a safe working directory:

```bash
mkdir -p ~/tlcl-ch31-case
cd ~/tlcl-ch31-case
```

---

# Exercise 1: Build the smallest `case` statement
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

---

# Exercise 2: Understand pattern matching
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

## Explain-back

Answer:

1. Why does `*.txt` match `notes.txt`? Shell pattern * matches any characters before .txt. 
2. Why does `[0-9]*` match `9-data.csv`? First character is a digit, * matches the rest. 
3. Is `[0-9]*` a regex or a shell pattern here? Shell pattern. 
4. What does the final `*)` branch do? It catches everything that wasn't matched earlier. 
5. Why is it useful to have a default branch? It handles unexpected input safely. 

---

# Exercise 3: First matching pattern wins
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

## Explain-back

Answer:

1. Did both patterns match logically? Yes. 
2. Which branch actually ran? *.log branch. 
3. Why does order matter? First match wins, and later matches are ignored. 
4. When should a more specific pattern come before a more general pattern? When the specific case must not be swallowed by a broad pattern. 

---

# Exercise 4: Compare `if` and `case`
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