# Day 2 — Command Substitution, Reading Input, and Arithmetic

## Read before exercises

Read the Effective Shell Ch. 10 sections on:

```text
Common Variable Operations
Storing a Command's Output in a Variable
Reading and Storing User Input in Variables
Performing Arithmetic Operations
Enhancing the common Command with Variables
```

If the chapter order in the book places arrays or parameter expansion before `read` and arithmetic, skim those headings but do not drill them yet. Day 3 handles them.

## What he should gain

He should gain this practical skill:

```text
I can capture command output, ask the user for input, and do simple integer arithmetic without guessing how Bash expands values.
```

This overlaps with TLCL Ch. 28 and Ch. 34, so keep it short and practical.

---

# Before reading: Feynman preview

Explain this before reading:

```text
A command normally prints to stdout.
Command substitution captures that stdout and stores it as text in a variable.
```

Example:

```bash
now=$(date)
```

Plain English:

```text
Run date, capture its output, and store that text in now.
```

---

# After reading: concept questions

Answer without looking back:

1. What does `$(command)` do?
2. What happens to trailing newlines in command substitution?
3. Why should the final variable expansion still usually be quoted?
4. What does `read` do?
5. Why is `read -r` usually safer than plain `read`?
6. What does `$(( ... ))` do?
7. Does Bash arithmetic handle floating-point numbers normally?
8. When should you stop using Bash and switch to Python for calculations?

Do not do the exercises until these are answered.

---

# Exercise 1: Capture command output

## Skill being gained

He is learning to store evidence from commands.

## Predict before typing

Predict what values will be stored:

```bash
host=$(hostname)
today=$(date +%F)
user_count=$(who | wc -l)
```

## Run

```bash
cd ~/effective-shell-ch10

host=$(hostname)
today=$(date +%F)
user_count=$(who | wc -l)

printf 'host=<%s>\n' "$host"
printf 'today=<%s>\n' "$today"
printf 'user_count=<%s>\n' "$user_count"
```

## Explain-back

Answer:

1. Which commands produced the values?
2. Were the values numbers or text from Bash's point of view?
3. Why are the expansions quoted in `printf`?
4. What would happen if a captured value contained spaces?

---

# Exercise 2: Build a tiny evidence script

## Skill being gained

He is learning to use variables to make a script clearer.

## Create

```bash
cat > evidence.sh <<'EOF'
#!/usr/bin/env bash

host=$(hostname)
today=$(date +%F)
kernel=$(uname -r)

printf 'Host: %s\n' "$host"
printf 'Date: %s\n' "$today"
printf 'Kernel: %s\n' "$kernel"
EOF

bash evidence.sh
```

## Explain-back

Answer:

1. Why store these values in variables instead of running commands directly inside `printf`?
2. Would direct commands also work?
3. Which version is easier to debug?
4. Which variables are temporary evidence?

---

# Exercise 3: Read input safely

## Skill being gained

He is learning to accept user input without treating backslashes specially.

## Predict before typing

Predict what the script asks and prints.

## Create

```bash
cat > ask-project.sh <<'EOF'
#!/usr/bin/env bash

read -r -p "Project name: " project
read -r -p "Server name: " server

printf 'Project=<%s>\n' "$project"
printf 'Server=<%s>\n' "$server"
printf 'Report file=<%s>\n' "${project}-${server}-report.txt"
EOF

bash ask-project.sh
```

Use test inputs with spaces:

```text
Project name: site 2
Server name: app01
```

## Explain-back

Answer:

1. Why did `${project}` need braces in `${project}-${server}-report.txt`?
2. Why are the variables quoted in `printf`?
3. Why use `read -r`?
4. What could go wrong if this script used the input as a filename without thought?

---

# Exercise 4: Integer arithmetic

## Skill being gained

He is learning Bash arithmetic for simple counters and checks.

## Predict before typing

Predict each value:

```bash
count=3
count=$((count + 1))
printf '%s\n' "$count"
printf '%s\n' "$((10 / 3))"
printf '%s\n' "$((10 % 3))"
```

## Run

```bash
count=3
count=$((count + 1))
printf 'count=<%s>\n' "$count"
printf '10 / 3 = <%s>\n' "$((10 / 3))"
printf '10 %% 3 = <%s>\n' "$((10 % 3))"
```

## Explain-back

Answer:

1. Why is `10 / 3` not `3.333...`?
2. What does `%` mean?
3. What kinds of arithmetic are okay in Bash?
4. What kinds should be done in another language?

---

# Day 2 finish standard

He is done only if he can explain:

```text
$(command) captures stdout as text.
read -r stores user input without backslash interpretation.
$(( ... )) performs integer arithmetic.
Variables still need careful quoting when expanded.
```
