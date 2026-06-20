# TLCL Chapter 24 Guide — Writing Your First Script
---
```

A script is like writing several steps on a recipe card:

```text
Step 1: gather ingredients
Step 2: mix
Step 3: cook
Step 4: serve
```

In shell terms:

```text
Step 1: print a heading
Step 2: show the date
Step 3: show the current user
Step 4: show disk usage
```

The computer follows the recipe exactly. If the recipe is unclear, dangerous, or wrong, the computer does not “understand what you meant.” It does what you wrote.

# One-time setup

Create a safe practice directory:

```bash
mkdir -p ~/tlcl-ch24-scripts/bin
cd ~/tlcl-ch24-scripts
```

Create a notes file:

```bash
cat > script-notes.txt <<'EOF'
Chapter 24: Writing Your First Script
A script is a saved list of shell commands.
EOF
```

Verify:

```bash
pwd
ls -l
cat script-notes.txt
```

Before moving on, answer:

```text
Where am I working? ~/tlcl-ch24-scripts
What files exist here? script-notes.txt. 
Why am I using a practice directory instead of random places in my home directory? Keep work isolated, safe, and organized. 
```

---

# Session 1 — What is a shell script?

## Read this part of Chapter 24

Read the opening part of Chapter 24 where Shotts introduces shell scripts and creates a first simple script.

Focus on:

```text
plain text files
commands saved in a file
running commands in order
using an editor
```
---

## Before typing: predict the manual commands

Suppose the goal is:

```text
Show a small system report.
```

Expected commands:

```bash
date
whoami
pwd
hostname
```

Now run them manually:

```bash
date
whoami
pwd
hostname
```

Question:

```text
If I can run these manually, why save them in a script?
```
To reuse them easily, run them together with one command, and document repeatable steps. 

---

## Exercise 1: Create the first script

Create a file:

```bash
cd ~/tlcl-ch24-scripts
nano hello-report
```

Or use Vim:

```bash
vim hello-report
```

Put this inside:

```bash
#!/bin/bash

echo "Hello from my first script"
echo "Today is:"
date
echo "I am running as:"
whoami
echo "The hostname is:"
hostname
```

Save and exit.

Before running it, inspect it:

```bash
cat hello-report
```

Now answer:

```text
Line 1 means: Shebang. Use /bin/bash to interpret. 
Line 3 means: Comment. This is ignored by the shell. 
Line 5 means: Prints text. 
Line 7 means: Runs the date command. 
Line 9 means: Runs the whoami command. 
```

---

## Exercise 2: Run with bash first

Run:

```bash
bash hello-report
```

Question:

```text
Why did this work even if the file is not executable yet?
```
Bash was told to interpret this file as a script. 
Verify permissions:

```bash
ls -l hello-report
```

Question:

```text
Does this file currently have execute permission?
```
No

---

## Session 1 explain-back

Explain this:

```text
A shell script is a plain text file containing a saved list of shell commands that run in order. 
The script is stored as a readable text file. 
I can inspect it with cat
I can run it with bash by typing bash script-name
The computer runs the commands in the same sequence. 
```

---

# Session 2 — Shebang, executable permission, and PATH

## Read this part of Chapter 24

Read the section where Shotts explains making a script executable and running it like a command.

Focus on:

```text
#!/bin/bash
chmod +x
./script-name
PATH
where scripts should live
```

---

## Feynman explanation before reading

The shebang line is like a label on the recipe card:

```text
Use this cook to follow the recipe.
```

In a script:

```bash
#!/bin/bash
```

means:

```text
Use bash to run this script.
```

---

## Before typing: predict permission behavior

Answer before running commands:

```text
If I type ./hello-report now, will it run? No. 
Why or why not? It's missing executable permissions.
What permission is probably missing? It's missing executable permissions. 
```

Test:

```bash
./hello-report
```

If it fails with permission denied, that is expected.

Now make it executable:

```bash
chmod +x hello-report
ls -l hello-report
```

Run:

```bash
./hello-report
```

---

## Exercise 3: Put the script in a personal bin directory

Create a personal bin directory:

```bash
mkdir -p ~/bin
cp hello-report ~/bin/
```

Check whether `~/bin` is in PATH:

```bash
echo "$PATH" | tr ':' '\n' | grep "$HOME/bin"
```

If `~/bin` is already in PATH, try:

```bash
hello-report
```

If not, run it by full path:

```bash
~/bin/hello-report
```

---

## Session 2 explain-back

Explain:

```text
#!/bin/bash uses bash to run this script(shebang)
chmod +x adds executable permission so the file can be run. 
./hello-report runs the script in the current directory. 
PATH is the list of directories the shell searches for commands. 
~/bin is useful because it's a standard personal directory for user scripts that can be added to path. 
```

---

# Session 3 — Improve the script

## Read this part of Chapter 24

Read the part of Chapter 24 where Shotts improves the first script and discusses writing scripts carefully.

Focus on:

```text
comments
readability
testing after small changes
building scripts gradually
```

---


## Exercise 4: Create a report script

Create:

```bash
cd ~/tlcl-ch24-scripts
nano system-snapshot
```

Put this inside:

```bash
#!/bin/bash

# system-snapshot: print a small identity and storage report.

echo "System Snapshot"
echo "==============="
echo

echo "Date:"
date
echo

echo "User:"
whoami
echo

echo "Host:"
hostname
echo

echo "Current directory:"
pwd
echo

echo "Disk usage:"
df -h | head
```

Before running, answer:

```text
Which lines print labels? The echo lines with text. 
Which lines run real commands? date, whoami, hostname, pwd, and df.
Which line uses a pipeline? df -h | head
What do I expect df -h | head to show? Human-readable disk usage summary for the first few filesystems. 
```

Run with bash first:

```bash
bash system-snapshot
```

Then make executable:

```bash
chmod +x system-snapshot
./system-snapshot
```

---

## Exercise 5: Modify one thing at a time

Add memory information if available:

```bash
free -h
```

But do not paste it into the script immediately.

First run manually:

```bash
free -h
```

Question:

```text
Does this command exist on this machine? Yes. 
What output does it show? Human readable memory and swap usage.
```

Now edit the script and add:

```bash
echo
echo "Memory:"
free -h
```

Run:

```bash
bash system-snapshot
```

---

## Exercise 6: Redirect output to a file

Run:

```bash
./system-snapshot > snapshot.txt
cat snapshot.txt
```

Question:

```text
Did the script change, or did the shell redirect its output?``` 
```
No. 
Explain:

```text
> overwrites or creates.
>> appends or creates.
```

---
# Common mistakes and what they teach

## Mistake 1: Permission denied

Command:

```bash
./hello-report
```

Possible error:

```text
Permission denied
```

Lesson:

```text
The file may not have execute permission.
```

Check:

```bash
ls -l hello-report
```

Fix:

```bash
chmod +x hello-report
```

---

## Mistake 2: Command not found

Command:

```bash
hello-report
```

Possible error:

```text
command not found
```

Lesson:

```text
The script may not be in a directory listed in PATH.
```

Run with explicit path:

```bash
./hello-report
```

or:

```bash
~/bin/hello-report
```

---

## Mistake 3: Bad shebang or missing interpreter

Check first line:

```bash
head -n 1 hello-report
```

Expected:

```bash
#!/bin/bash
```

Lesson:

```text
The shebang tells the system what interpreter should run the script.
```

---

## Mistake 4: Editing without testing

Bad habit:

```text
Add ten lines, then run the script.
```

Better habit:

```text
Add one small section.
Run it.
Inspect output.
Then continue.
```

---

# Mini-lab: Write a useful script from scratch

Goal:

```text
Create a script called lab-check that prints basic information about the current machine.
```

Required output sections:

```text
Title
Date
User
Host
Working directory
IP address summary
Disk summary
```

Possible commands:

```bash
date
whoami
hostname
pwd
ip -brief addr
df -h | head
```

Rules:

```text
1. Run each command manually first.
2. Write the script second.
3. Run with bash script-name third.
4. Add execute permission fourth.
5. Run with ./script-name fifth.
6. Redirect output to a report file sixth.
```

Create:

```bash
cd ~/tlcl-ch24-scripts
nano lab-check
```

Skeleton:

```bash
#!/bin/bash

# lab-check: print a small report about this machine.

echo "Lab Check"
echo "========="
echo

# Add sections below one at a time.
```

After finishing, run:

```bash
bash lab-check
chmod +x lab-check
./lab-check
./lab-check > lab-check-report.txt
cat lab-check-report.txt
```

Final explanation:

```text
This script helps me because...
The safest way to test it was...
The command most likely to differ between machines is...
The line I understand least is...
```

---

# End-of-chapter self-test

1. What is a shell script? A plain text file containing a sequence of shell commands. 
2. Why does a script start with `#!/bin/bash`? It tells the system which interpreter to use. 
3. What does `chmod +x script-name` do? It makes the file executable. 
4. Why might `./script-name` work when `script-name` does not? ./ tells the shell to look in the current directory. 
5. How can you run a script without execute permission? bash script-name or sh script-name. 
6. Why should you inspect a script before running it? To verify it's safe and does what you expect. 
7. Why should you test after small changes? Makes debugging much easier. 
8. How do you save script output to a file? ./script > file.txt
9. What is the difference between `>` and `>>`? > overwrites and >> appends. 
10. What makes a script readable? Comments, clear labels with echo, consistent formatting, and small logical sections 
