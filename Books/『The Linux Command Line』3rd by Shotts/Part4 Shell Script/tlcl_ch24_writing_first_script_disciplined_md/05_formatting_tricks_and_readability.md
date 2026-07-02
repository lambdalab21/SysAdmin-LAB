# Shotts Chapter 24: Writing Your First Script

Use this guide with William Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

This chapter starts Part IV of the book: shell scripting. The goal is not to type a few scripts and feel finished. The goal is to learn how a command sequence becomes a repeatable tool.

Core discipline:

```text
Do not type first.
Think first.
Write the English purpose first.
Predict what the script should do.
Run it safely.
Verify the result.
Explain every line.
```

One-time lab setup:

```bash
mkdir -p ~/tlcl-ch24-lab/{bin,scripts,tmp,output}
cd ~/tlcl-ch24-lab
```

Use this answer template throughout the chapter:

```text
Purpose of this script:
Input it uses:
Output it produces:
Commands inside it:
How I will run it:
How I will verify it worked:
What could go wrong:
```

---
# Session 5: Formatting Tricks, Long Option Names, and Readability

## Read before exercise

Read these Chapter 24 sections:

```text
More Formatting Tricks
Long Option Names
Indentation and Line Continuation
```

## What he should gain from this reading

He should gain this idea:

```text
Scripts are read by humans more often than they are written.
Readable scripts are safer scripts.
```

This matters even in a beginner script.

---

# Feynman analogy

A script is like instructions left for your future self.

If the instructions are messy, future you will make mistakes.

Formatting is not decoration. It reduces confusion.

---

# After-reading questions

Answer before editing:

1. Why are comments useful?
2. Why are blank lines useful?
3. Why might long option names be clearer than short options?
4. What is line continuation?
5. Why should indentation be consistent?

---

# Exercise 1: Improve script readability

Edit:

```bash
nano ~/tlcl-ch24-lab/scripts/system-info
```

Make it more readable:

```bash
#!/usr/bin/env bash

# Print basic information about the current shell session.

printf 'System information
'
printf '==================
'

printf 'Host:      %s
' "$(hostname)"
printf 'User:      %s
' "$(whoami)"
printf 'Directory: %s
' "$(pwd)"
printf 'Date:      %s
' "$(date)"
```

---

# Predict before running

Before running:

1. What changed from `echo` to `printf`?
2. What does `$(hostname)` do?
3. Why are command substitutions quoted?
4. What output format do you expect?

Run:

```bash
bash ~/tlcl-ch24-lab/scripts/system-info
```

---

# Exercise 2: Use help and man

Run:

```bash
help printf
printf --help 2>/dev/null | head -n 20 || true
man printf
```

Important distinction:

```text
Bash has a built-in printf.
The system may also have /usr/bin/printf.
```

Run:

```bash
type printf
```

Answer:

1. Is `printf` a shell builtin?
2. Which help source was fastest?
3. What does `%s` mean?
4. What does `
` mean?

---

# Exercise 3: Long option names

Create a second script:

```bash
cat > ~/tlcl-ch24-lab/scripts/list-lab <<'EOF'
#!/usr/bin/env bash

# List the Chapter 24 lab directory in a readable way.

ls --all --human-readable --long ~/tlcl-ch24-lab
EOF

chmod +x ~/tlcl-ch24-lab/scripts/list-lab
```

Before running:

```text
What does --all mean?
What does --human-readable mean?
What does --long mean?
How can I confirm?
```

Use documentation:

```bash
ls --help | grep -- '--all'
ls --help | grep -- '--human-readable'
ls --help | grep -- '--long'
man ls
```

Run:

```bash
~/tlcl-ch24-lab/scripts/list-lab
```

---

# Exercise 4: Line continuation

Edit `list-lab`:

```bash
nano ~/tlcl-ch24-lab/scripts/list-lab
```

Change it to:

```bash
#!/usr/bin/env bash

# List the Chapter 24 lab directory in a readable way.

ls   --all   --human-readable   --long   ~/tlcl-ch24-lab
```

Before running:

```text
What does the backslash at the end of a line do?
Why is this easier to read?
What mistake would break this command?
```

Run:

```bash
~/tlcl-ch24-lab/scripts/list-lab
```

---

# Explain-back

Explain this script:

```bash
ls   --all   --human-readable   --long   ~/tlcl-ch24-lab
```

Use:

```text
The command is still one command because...
Each long option means...
The indentation helps because...
```

---

# Read after exercise

Reread the formatting sections.

This time, answer:

```text
Which formatting habit will prevent the most future mistakes for me?
```

Choose one:

```text
comments
blank lines
long option names
indentation
line continuation
```

Explain why.

---

# Session 5 checkpoint

He is ready for the final lab only if he can answer:

1. Why use comments?
2. Why might long options be better for beginner scripts?
3. What does line continuation do?
4. Why is `printf` often more controlled than `echo`?
5. How did he use `man` or `--help` to verify an option?
