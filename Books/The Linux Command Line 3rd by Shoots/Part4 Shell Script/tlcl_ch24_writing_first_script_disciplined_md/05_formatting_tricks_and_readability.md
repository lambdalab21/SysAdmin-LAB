# Shotts Chapter 24: Writing Your First Script

Use this guide with William Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

One-time lab setup:

```bash
mkdir -p ~/tlcl-ch24-lab/{bin,scripts,tmp,output}
cd ~/tlcl-ch24-lab
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

# After-reading questions

Answer before editing:

1. Why are comments useful? Comments explain why code exists. 
2. Why are blank lines useful? Black lines separate logical sections. 
3. Why might long option names be clearer than short options? Long options are self-documenting. 
4. What is line continuation? Line continuation joins split lines into one command. 
5. Why should indentation be consistent? Consistent indentation shows structure and nesting clearly. 

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

1. What changed from `echo` to `printf`? printf gives exact control over newlines and formatting. 
2. What does `$(hostname)` do? $(hostname) runs the command and inserts its output. 
3. Why are command substitutions quoted? It's quoted to prevent word-splitting if output has spaces. 
4. What output format do you expect? Labeled blocks with aligned colons. 

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

1. Is `printf` a shell builtin? Yes. 
2. Which help source was fastest? help printf
3. What does `%s` mean? string placeholder. 
4. What does \n is a newline.`
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
What does --all mean? Shows hidden files. 
What does --human-readable mean? shows sizes in kb/md/etc.
What does --long mean? use -l instead. 
How can I confirm? confirmed via ls --help. 
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
What does the backslash at the end of a line do? backslash at the end of a line continues to the command. 
Why is this easier to read? It's easier to read because it has one option per line. 
What mistake would break this command? Trailing space after \ or missing \ entirely. 
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
The command is still one command because \ joins lines. 
Each long option is self-explanatory. 
The indentation helps because it provides visual alignment of options. 
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

chosen: long option names. 
They make scripts self-documenting. There is no need to remember short flags or to look them up later. Reduces mistakes for beginners. 

---

# Session 5 checkpoint

He is ready for the final lab only if he can answer:

1. Why use comments? Comments are used to record intent and make maintenance easier. 
2. Why might long options be better for beginner scripts? Long options are clearer and less error-prone for beginners. 
3. What does line continuation do? It joins multi-line commands into one logical line.
4. Why is `printf` often more controlled than `echo`? printf offers precise formatting and reliable newlines. 
5. How did he use `man` or `--help` to verify an option? Used command --help and man command to verify options. 
