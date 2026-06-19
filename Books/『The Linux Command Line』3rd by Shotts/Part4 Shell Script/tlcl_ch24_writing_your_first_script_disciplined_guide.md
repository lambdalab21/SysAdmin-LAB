# TLCL Chapter 24 Guide — Writing Your First Script

A cleaner phrasing: “Please create a downloadable Markdown file to guide reading Chapter 24, ‘Writing Your First Script.’ Use the Feynman method if applicable, and make sure he does not just type the instructions mindlessly.”

Use this guide with William Shotts’s *The Linux Command Line*, Chapter 24.

Chapter 24 is the beginning of shell scripting. The goal is not to “type a script from the book.” The goal is to understand that a script is a saved sequence of shell commands that can be inspected, improved, tested, and reused.

---

# Main discipline for this chapter

Before running any script, the student must answer:

```text
1. What problem is this script supposed to solve?
2. What command would I type manually to solve it once?
3. What lines are in the script?
4. What does each line do?
5. What output do I expect?
6. What could go wrong?
7. How will I verify the result?
```

Rule:

```text
If I cannot explain the script line by line, I should not run it yet.
```

---

# Feynman analogy: a script is a recipe

A shell script is like a cooking recipe.

A command typed at the prompt is like saying:

```text
Do this one step now.
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

---

# Suggested reading split

Do not read Chapter 24 as one passive reading. Split it into three sessions.

| Session | Reading focus | Practice focus |
|---|---|---|
| Session 1 | What a script is; creating the first script | Write, read, and run a tiny script |
| Session 2 | Shebang, executable permission, PATH | Understand how the shell finds and runs scripts |
| Session 3 | Editing, testing, improving, and debugging habits | Build a useful mini-report script |

If he is comfortable, this can be done in 2–3 days. If he is new to scripting, use 4–5 days with review.

---

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
Where am I working?
What files exist here?
Why am I using a practice directory instead of random places in my home directory?
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

Do not worry yet about making the script executable. First understand that the script is just text.

---

## Feynman explanation before reading

Explain this to a ten-year-old:

```text
When I type commands one at a time, I am giving the computer instructions now.
When I put those commands in a file, I am saving the instructions so I can run them again later.
That file is called a script.
```

---

## Before typing: predict the manual commands

Suppose the goal is:

```text
Show a small system report.
```

What manual commands could answer this?

Write your guess first:

```text
Command to show current date:
Command to show current user:
Command to show current directory:
Command to show hostname:
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

Expected idea:

```text
A script lets me repeat the same steps reliably.
```

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
Line 1 means:
Line 3 means:
Line 5 means:
Line 7 means:
Line 9 means:
```

Do not continue until he can explain each line.

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

Expected answer:

```text
Because I explicitly asked bash to read and run the file.
```

Verify permissions:

```bash
ls -l hello-report
```

Question:

```text
Does this file currently have execute permission?
```

---

## Session 1 explain-back

Explain this without notes:

```text
A shell script is...
The script is stored as...
I can inspect it with...
I can run it with bash by typing...
The computer runs the commands in...
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

Executable permission is like permission to use the recipe card as an action, not just read it.

`PATH` is like a list of shelves where the shell looks for commands.

---

## Before typing: predict permission behavior

Answer before running commands:

```text
If I type ./hello-report now, will it run?
Why or why not?
What permission is probably missing?
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

## Discipline checkpoint: explain `./`

Question:

```text
Why do I type ./hello-report instead of just hello-report?
```

Expected idea:

```text
The current directory is usually not in PATH. ./ tells the shell to run the file from the current directory.
```

Check:

```bash
echo "$PATH"
```

Ask:

```text
Do I see . as a PATH entry?
Should I add . to PATH casually?
```

Expected answer:

```text
No. Adding . to PATH can be unsafe because the shell might run a local file accidentally.
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

Do not edit startup files yet unless the chapter tells him to and he understands what he is changing.

---

## Session 2 explain-back

Explain:

```text
#!/bin/bash means...
chmod +x means...
./hello-report means...
PATH is...
~/bin is useful because...
```

---

# Session 3 — Improve the script without mindless typing

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

## Feynman explanation before reading

A good script is not just a working script.

A good script is like a clear recipe:

```text
another person can read it
future me can understand it
mistakes are easier to find
```

Comments are not for explaining obvious commands. They are for explaining purpose.

Bad comment:

```bash
# echo prints text
echo "Hello"
```

Better comment:

```bash
# Print a short identity report for this machine.
echo "Hello"
```

---

## Exercise 4: Create a disciplined report script

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
Which lines print labels?
Which lines run real commands?
Which line uses a pipeline?
What do I expect df -h | head to show?
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
Does this command exist on this machine?
What output does it show?
Where should it go in the report?
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
Did the script change, or did the shell redirect its output?
```

Expected answer:

```text
The shell redirected the script's output into snapshot.txt.
```

Now append two snapshots:

```bash
./system-snapshot >> snapshot-history.txt
./system-snapshot >> snapshot-history.txt
cat snapshot-history.txt
```

Explain:

```text
> overwrites or creates.
>> appends or creates.
```

---

# Anti-mindless-typing gates

Use these gates throughout Chapter 24.

## Gate 1: Line-by-line translation

Before running a script, he must fill this out:

```text
Script name:
Purpose:
Line 1:
Line 2:
Line 3:
Line 4:
Command I expect to produce output:
Command I expect to possibly fail:
```

## Gate 2: Prediction before execution

Before running:

```text
I expect the output to include:
I expect the script to create these files:
I expect the script not to modify:
```

## Gate 3: Verification after execution

After running:

```text
Did the output match my prediction?
What surprised me?
Did any files change?
How do I know?
```

## Gate 4: Explain to a younger student

He must explain:

```text
What is a script?
What is a shebang?
Why use chmod +x?
Why use ./script-name?
Why test with bash script-name first?
```

If he cannot explain these, he should reread the relevant part of the chapter.

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

Without notes, answer:

1. What is a shell script?
2. Why does a script start with `#!/bin/bash`?
3. What does `chmod +x script-name` do?
4. Why might `./script-name` work when `script-name` does not?
5. How can you run a script without execute permission?
6. Why should you inspect a script before running it?
7. Why should you test after small changes?
8. How do you save script output to a file?
9. What is the difference between `>` and `>>`?
10. What makes a script readable?

Expected answers:

```text
1. A text file containing commands for the shell to run.
2. It tells the system to use bash as the interpreter.
3. It adds execute permission.
4. The current directory is usually not in PATH; ./ gives an explicit path.
5. Use bash script-name.
6. Scripts can run many commands and may change files.
7. Small changes make mistakes easier to find.
8. script-name > file.txt
9. > overwrites; >> appends.
10. Clear purpose, simple commands, comments where useful, and tested sections.
```

---

# Final standard

He understands Chapter 24 only if he can say:

```text
I know that a script is a plain text file of commands.
I can explain the shebang.
I can run a script with bash or with execute permission.
I know why ./ is needed.
I can build a script one small section at a time.
I can redirect script output.
I do not run scripts I cannot explain.
```
