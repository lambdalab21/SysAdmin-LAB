# Shotts Chapter 24: Writing Your First Script

Use this guide with William Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

One-time lab setup:

```bash
mkdir -p ~/tlcl-ch24-lab/{bin,scripts,tmp,output}
cd ~/tlcl-ch24-lab
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
# After-reading questions

Answer before editing the script:

1. What is a shebang? A first line that tells the system which interpreter to use. 
2. Why does it start with `#!`? It's the 'magic number' that the kernel recognizes as a script derivative. 
3. What does the shebang tell the system? which program should execute the script. 
4. Why should the shebang be the first line? The kernel only checks the very beginning of the file for it. 
5. What is the difference between running `bash scriptname` and running `./scriptname`? `bash scriptname` focuses bash to run it. 

---

# Exercise 1: Inspect the current script

Run:

```bash
cd ~/tlcl-ch24-lab/scripts
nl -ba system-info
```

Answer:

```text
Does the script have a shebang? No. 
What is line 1? A comment. 
What line contains the purpose comment? Line 1. 
```

---

# Exercise 2: Add a shebang

Edit the script:

```bash
nano system-info
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
No. Bash is still explicitly running the file. 
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

1. What does `env` do in simple terms? It runs a command with a modified environment; commonly used to find programs in $PATH. 
2. Why might `#!/usr/bin/env bash` be used? It finds bash anywhere in the user's path. 
3. What command told you where `bash` is? `which base` or `command -v bash`

---


# Session 2 checkpoint

He is ready for Session 3 only if he can answer:

1. What is the shebang? The #! line at the top specifying the interpreter. 
2. Why should it be first? The kernel reads it only from the very  start of the file. 
3. Why did `bash system-info` work before executable permission was set? You told bash directly to run the file as a script. 
4. What will be different when running `./system-info`? The system will use the shebang to choose file interpreter. 