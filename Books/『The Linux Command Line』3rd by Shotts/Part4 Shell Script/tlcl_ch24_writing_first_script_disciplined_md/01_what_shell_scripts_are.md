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
# Session 1: What Shell Scripts Are

## Read before exercise

Read these sections in Chapter 24:

```text
What Are Shell Scripts?
How to Write a Shell Script
```

## What he should gain from this reading

He should gain this idea:

```text
A shell script is a file containing commands that the shell can read and execute.
```

He is not learning a new mysterious language yet. He is learning how to save command-line work into a file.

---

# Feynman analogy

Imagine he does the same morning checklist every day:

```text
open backpack
check laptop
check charger
check notebook
leave house
```

Instead of remembering every step each morning, he writes a checklist.

A shell script is similar:

```text
manual commands become a repeatable checklist for the shell
```

---

# After-reading questions

Answer before touching the keyboard:

1. In the simplest terms, what is a shell script?
2. Who reads the script file?
3. Why would a script be better than typing the same commands repeatedly?
4. What is the danger of copying a script without understanding it?
5. What should be written before the first line of code: the command or the purpose?

Expected core answer:

```text
A shell script is a plain text file containing commands. The shell reads the file and runs the commands.
```

---

# Exercise 1: Turn manual commands into a script idea

Do not write a script yet.

First run these commands manually:

```bash
cd ~/tlcl-ch24-lab
hostname
whoami
pwd
date
```

Now answer:

```text
What did each command tell me?
Would these commands be useful together?
What would I call a script that runs them?
```

Possible purpose statement:

```text
This script prints basic information about the current machine, user, directory, and time.
```

---

# Exercise 2: Write the purpose before the script

Create a script file:

```bash
cd ~/tlcl-ch24-lab/scripts
nano system-info
```

Before writing commands, put this comment at the top:

```bash
# Print basic information about the current shell session.
```

Then add:

```bash
echo "System information"
echo "=================="
hostname
whoami
pwd
date
```

Save and exit.

Do not run it yet.

---

# Predict before running

Answer:

1. What lines should appear first?
2. Which command will print the machine name?
3. Which command will print the username?
4. Which command will print the current directory?
5. Which command will print the current date/time?

Now run it with `bash`:

```bash
bash system-info
```

---

# Explain-back

Explain the script in this format:

```text
The purpose of this script is...
The first echo prints...
hostname prints...
whoami prints...
pwd prints...
date prints...
```

If he cannot explain every line, he should not move on.

---

# Read after exercise

Reread the “How to Write a Shell Script” section.

This time, read it with this question in mind:

```text
What process does Shotts want me to follow when creating a script?
```

Write a three-step process:

```text
1.
2.
3.
```

---

# Session 1 checkpoint

He is ready for Session 2 only if he can answer:

1. What is a shell script?
2. Why should the purpose be written before the commands?
3. Why did `bash system-info` work even though the file is not executable yet?
4. What is the difference between typing commands manually and saving them in a script?
