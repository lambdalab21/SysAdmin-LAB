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
# Session 2: Script File Format and the Shebang

## Read before exercise

Read this Chapter 24 section:

```text
Script File Format
```

## What he should gain from this reading

He should gain this idea:

```text
A script should clearly state which interpreter should run it.
```

The key line is the shebang:

```bash
#!/usr/bin/env bash
```

or, on many systems:

```bash
#!/bin/bash
```

This guide uses:

```bash
#!/usr/bin/env bash
```

because it asks the environment to locate `bash`.

---

# Feynman analogy

A script is like a worksheet.

The shebang is like writing at the top:

```text
Give this worksheet to the Bash teacher.
```

Without that instruction, the system may not know which interpreter should read the file when the file is executed directly.

---

# After-reading questions

Answer before editing the script:

1. What is a shebang?
2. Why does it start with `#!`?
3. What does the shebang tell the system?
4. Why should the shebang be the first line?
5. What is the difference between running `bash scriptname` and running `./scriptname`?

---

# Exercise 1: Inspect the current script

Run:

```bash
cd ~/tlcl-ch24-lab/scripts
nl -ba system-info
```

Answer:

```text
Does the script have a shebang?
What is line 1?
What line contains the purpose comment?
```

---

# Exercise 2: Add a shebang

Edit the script:

```bash
vim system-info
```

Make it look like this:

```bash
#!/usr/bin/env bash

# Print basic information about the current shell session.

echo "System information"
echo "=================="
hostname
whoami
pwd
date
```

Save and exit.

---

# Predict before running

Before running, answer:

```text
Will adding the shebang change the output when I run bash system-info?
Why or why not?
```

Run:

```bash
bash system-info
```

Expected idea:

```text
The output should be the same because bash is still explicitly running the file.
```

---

# Exercise 3: Ask the system where bash is

Run:

```bash
which bash
command -v bash
/usr/bin/env bash --version | head -n 1
```

Then use documentation:

```bash
man env
```

Inside `man`, search for `SYNOPSIS` and `DESCRIPTION`.

Answer:

1. What does `env` do in simple terms?
2. Why might `#!/usr/bin/env bash` be used?
3. What command told you where `bash` is?

---

# Explain-back

Explain this line to a younger student:

```bash
#!/usr/bin/env bash
```

Use this format:

```text
#! means...
/usr/bin/env means...
bash means...
The whole line tells the system to...
```

---

# Read after exercise

Reread “Script File Format.”

This time, answer:

```text
What formatting habits make a script easier to understand later?
```

List at least three:

```text
1.
2.
3.
```

---

# Session 2 checkpoint

He is ready for Session 3 only if he can answer:

1. What is the shebang?
2. Why should it be first?
3. Why did `bash system-info` work before executable permission was set?
4. What will be different when running `./system-info`?
