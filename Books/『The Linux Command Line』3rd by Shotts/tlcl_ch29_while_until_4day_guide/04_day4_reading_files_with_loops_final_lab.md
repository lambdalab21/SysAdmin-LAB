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
# Day 4: Reading Files with Loops and Final Lab

## Read before exercises

Read the Chapter 29 sections:

```text
Reading Files with Loops
Summing Up
```

## What he should gain

He should gain this idea:

```text
A while loop can process input one line at a time.
This is useful for logs, config files, host lists, and generated command output.
```

This is one of the most practical shell scripting patterns.

---

# Before reading: Feynman preview

Imagine a stack of index cards.

```text
Read one card.
Process it.
Read the next card.
Stop when there are no cards left.
```

That is the basic file-reading loop.

In Bash:

```bash
while read -r line; do
    commands using "$line"
done < file
```

---

# After reading: concept questions

Answer without looking back:

1. What does `read -r line` do?
2. Why is `-r` usually safer than plain `read`?
3. What does `done < file` do?
4. Why should `"$line"` usually be quoted?
5. What happens when the file has no more lines?
6. Why is line-by-line processing useful for sysadmin work?
7. What kind of file should you avoid parsing with fragile shell code?

---

# Exercise 1: Read a file line by line

## Skill being gained

He is learning the standard input-redirection loop pattern.

## Setup

```bash
cd ~/tlcl-ch29-loops

cat > hosts.txt <<'EOF'
app01
app02
db01
playground01
EOF
```

## Do not type yet

Predict:

```text
How many times will the loop run?
What value will host have each time?
What stops the loop?
```

## Create script

```bash
cat > read-hosts.sh <<'EOF'
#!/usr/bin/env bash

while read -r host; do
    echo "Host: $host"
done < hosts.txt
EOF

bash read-hosts.sh
```

## Explain-back

Answer:

1. Where does `read` get input from?
2. What variable receives each line?
3. Why does the loop stop?
4. What would change if `hosts.txt` had 100 lines?

---

# Exercise 2: Ignore blank lines and comments

## Skill being gained

He is learning to combine loops with branching.

## Setup

```bash
cat > hosts-with-comments.txt <<'EOF'
# production-ish hosts
app01
app02

# database
db01

# lab machine
playground01
EOF
```

## Do not type yet

Predict:

```text
Which lines should be processed?
Which lines should be skipped?
Should this use break or continue?
```

## Create script

```bash
cat > read-clean-hosts.sh <<'EOF'
#!/usr/bin/env bash

while read -r host; do
    if [[ -z "$host" ]]; then
        continue
    fi

    if [[ "$host" == \#* ]]; then
        continue
    fi

    echo "Would inspect host: $host"
done < hosts-with-comments.txt
EOF

bash read-clean-hosts.sh
```

## Explain-back

Answer:

1. What does `[[ -z "$host" ]]` test?
2. What does `[[ "$host" == \#* ]]` test?
3. Is `\#*` a regex or a glob pattern?
4. Why is `continue` correct here instead of `break`?
5. What lines actually reached the final `echo`?

---

# Exercise 3: Final lab — loop-driven report menu

## Skill being gained

He is combining Chapters 27, 28, and 29:

```text
if/case thinking
+ read input
+ loops
+ file processing
```

## Read before exercise

Reread Chapter 29 “Summing Up.”

Ask:

```text
What loop controls the menu?
What loop processes the file?
What branches handle bad input?
```

## Do not type yet

Write this plan:

```text
Main menu loop condition:
Quit input:
File to read:
Lines to skip:
Commands that only preview action:
```

## Create script

```bash
cat > lab-menu-final.sh <<'EOF'
#!/usr/bin/env bash

hosts_file="hosts-with-comments.txt"

show_hosts () {
    echo "Configured hosts:"
    while read -r host; do
        if [[ -z "$host" ]]; then
            continue
        fi
        if [[ "$host" == \#* ]]; then
            continue
        fi
        echo "- $host"
    done < "$hosts_file"
}

show_system_info () {
    echo "System: $(hostname)"
    echo "Date: $(date)"
}

while true; do
    echo
    echo "Lab Menu"
    echo "1. Show system info"
    echo "2. Show hosts from file"
    echo "q. Quit"
    read -r -p "Choice: " choice

    if [[ -z "$choice" ]]; then
        echo "Please enter a choice."
        continue
    fi

    if [[ "$choice" == "q" ]]; then
        echo "Goodbye."
        break
    elif [[ "$choice" == "1" ]]; then
        show_system_info
    elif [[ "$choice" == "2" ]]; then
        show_hosts
    else
        echo "Unknown choice: $choice"
    fi
done
EOF

bash -n lab-menu-final.sh
bash lab-menu-final.sh
```

Test inputs:

```text
empty Enter
1
2
x
q
```

## Explain-back

Answer:

1. Which loop is the menu loop?
2. Which loop is the file-reading loop?
3. Which inputs cause `continue`?
4. Which input causes `break`?
5. What file does the inner loop read?
6. Why is `"$hosts_file"` quoted?
7. Why is the script using preview output instead of real SSH commands?

---

# Exercise 4: Add one safe improvement

Choose one improvement:

```text
A. Add a warning if the hosts file does not exist.
B. Count how many hosts were processed.
C. Add another menu option for disk space.
```

Before typing, write:

```text
What function changes?
What condition changes?
What test cases will prove it works?
```

Example for A:

```bash
show_hosts () {
    if [[ ! -f "$hosts_file" ]]; then
        echo "Missing hosts file: $hosts_file"
        return 1
    fi

    echo "Configured hosts:"
    while read -r host; do
        if [[ -z "$host" ]]; then
            continue
        fi
        if [[ "$host" == \#* ]]; then
            continue
        fi
        echo "- $host"
    done < "$hosts_file"
}
```

Test both cases:

```bash
bash lab-menu-final.sh
mv hosts-with-comments.txt hosts-with-comments.txt.bak
bash lab-menu-final.sh
mv hosts-with-comments.txt.bak hosts-with-comments.txt
```

---

# Final concept questions

Answer in writing:

1. What is a `while` loop?
2. What is an `until` loop?
3. What is the difference between `break` and `continue`?
4. What does `read -r` do?
5. What does `done < file` do?
6. Why can loops become infinite?
7. How do you test both the normal path and failure path?
8. Why should dangerous commands inside loops first be replaced with `echo`?
9. Why is line-by-line file processing useful?
10. What did Chapter 29 add to Chapters 27 and 28?

---

# Final Feynman explain-back

Explain this to a younger student:

```text
An if statement chooses once.
A loop chooses repeatedly.
A while loop keeps running while a condition is true.
An until loop keeps running until a condition becomes true.
break stops the loop.
continue skips the rest of the current pass.
A while-read loop can process a file one line at a time.
```

Then explain `lab-menu-final.sh` line by line.

If he cannot explain why the loop stops, he is not ready to move on.

---

# Day 4 finish standard

He is done with Chapter 29 only if he can say:

```text
I can write a while loop from scratch.
I can choose while or until based on the English condition.
I can use break and continue deliberately.
I can read a file line by line safely.
I can test valid input, invalid input, empty input, missing files, and quit behavior.
```
