#Book/The-Linux-Command-Line #Author/Shotts 
#shell-script
# Author/Shotts Chapter 24: Writing Your First Script

Use this guide with William Author/Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

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
# Session 3: Executable Permissions and Running Scripts

## Read before exercise

Read this Chapter 24 section:

```text
Executable Permissions
```

## What he should gain from this reading

He should gain this idea:

```text
A script file is not automatically a runnable program.
It needs executable permission if you want to run it directly.
```

---

# Feynman analogy

A script file is like a written recipe.

Executable permission is like permission to hand the recipe to the kitchen and say:

```text
Run this.
```

Without executable permission, the file may be readable but not directly runnable.

---

# After-reading questions

Answer before typing:

1. What does executable permission allow?
2. Which command changes file permissions?
3. What does `chmod +x scriptname` mean?
4. How can `ls -l` show whether a file is executable?
5. Why is `./system-info` different from `system-info`?

---

# Exercise 1: Inspect permissions

Run:

```bash
cd ~/tlcl-ch24-lab/scripts
ls -l system-info
```

Answer:

```text
Owner permissions:
Group permissions:
Other permissions:
Is x present?
```

---

# Exercise 2: Try direct execution before chmod

Predict first:

```text
What do I expect ./system-info to do before chmod +x?
```

Run:

```bash
./system-info
```

If permission is denied, that is expected.

Now answer:

```text
Did the error come from Bash syntax or file permission?
How do I know?
```

---

# Exercise 3: Add executable permission

Run:

```bash
chmod +x system-info
ls -l system-info
```

Now predict:

```text
What changed in ls -l?
Will ./system-info run now?
```

Run:

```bash
./system-info
```

---

# Exercise 4: Compare three ways to run

Run:

```bash
bash system-info
./system-info
system-info
```

The last one will probably fail unless the script directory is in `PATH`.

Fill in:

```text
bash system-info worked because...
./system-info worked because...
system-info failed or worked because...
```

---

# Documentation habit

Use:

```bash
man chmod
chmod --help | head -n 20
```

Answer:

1. What does `chmod` change?
2. What does `+x` add?
3. What section of `man chmod` was most useful?
4. Was `--help` faster for quick syntax?

---

# Explain-back

Explain:

```bash
chmod +x system-info
./system-info
```

Use:

```text
chmod +x means...
./ means...
The system reads the shebang and...
```

---

# Read after exercise

Reread “Executable Permissions.”

This time, read with this question:

```text
Why is executable permission a safety feature?
```

Write a short answer.

---

# Session 3 checkpoint

He is ready for Session 4 only if he can answer:

1. Why did `bash system-info` work without executable permission?
2. Why did `./system-info` need executable permission?
3. What does `chmod +x` do?
4. Why does `system-info` not automatically work from the current directory?
