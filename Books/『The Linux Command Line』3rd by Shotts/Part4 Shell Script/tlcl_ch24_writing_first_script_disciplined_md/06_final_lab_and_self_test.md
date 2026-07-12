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
# Session 6: Final Lab and Self-Test

## Read before exercise

Read this Chapter 24 section:

```text
Summing Up
```

Then skim the whole chapter again, especially:

```text
What Are Shell Scripts?
Script File Format
Executable Permissions
Script File Location
Good Locations for Scripts
More Formatting Tricks
Long Option Names
Indentation and Line Continuation
```

## What he should gain from this review

He should see the whole chapter as one workflow:

```text
write a purpose
create a text file
add a shebang
write commands clearly
run with bash while testing
make executable
put it in a sensible location
verify which script is running
format it for future readability
```

---

# Final Lab: Write a real beginner script

Goal:

```text
Create a script called lab-check that summarizes the current lab environment.
```

It should show:

```text
hostname
user
current directory
current date
number of files in ~/tlcl-ch24-lab
where bash is located
```

---

# Step 1: Write the purpose first

Before creating the file, fill this out:

```text
Script name:
Purpose:
Commands I probably need:
Expected output:
How I will verify it:
```

---

# Step 2: Draft the script

Create:

```bash
vim ~/tlcl-ch24-lab/scripts/lab-check
```

Write:

```bash
#!/usr/bin/env bash

# Summarize the current Chapter 24 lab environment.

printf 'Lab check
'
printf '=========
'
printf '
'

printf 'Host:        %s
' "$(hostname)"
printf 'User:        %s
' "$(whoami)"
printf 'Directory:   %s
' "$(pwd)"
printf 'Date:        %s
' "$(date)"
printf 'Bash path:   %s
' "$(command -v bash)"
printf 'Lab files:   %s
' "$(find ~/tlcl-ch24-lab -type f | wc -l)"
```

---

# Step 3: Predict before running

Answer:

1. What should the first two lines be?
2. Which command substitution prints the hostname?
3. Which command substitution counts files?
4. What could go wrong with `find ~/tlcl-ch24-lab -type f | wc -l`?
5. Is this script changing the system or only printing information?

---

# Step 4: Run safely with bash first

Run:

```bash
bash ~/tlcl-ch24-lab/scripts/lab-check
```

If there is an error, do not randomly edit.

Use this debugging checklist:

```text
Which line failed?
Is there a missing quote?
Is there a missing parenthesis?
Did I type the file path correctly?
Can I run the inner command by itself?
```

Test inner commands individually:

```bash
hostname
whoami
pwd
date
command -v bash
find ~/tlcl-ch24-lab -type f | wc -l
```

---

# Step 5: Make executable

Run:

```bash
chmod +x ~/tlcl-ch24-lab/scripts/lab-check
ls -l ~/tlcl-ch24-lab/scripts/lab-check
```

Run directly:

```bash
~/tlcl-ch24-lab/scripts/lab-check
```

---

# Step 6: Optional PATH practice

Copy to `~/bin`:

```bash
mkdir -p ~/bin
cp ~/tlcl-ch24-lab/scripts/lab-check ~/bin/lab-check
chmod +x ~/bin/lab-check
```

Temporarily add `~/bin` to PATH if needed:

```bash
export PATH="$HOME/bin:$PATH"
```

Verify:

```bash
type lab-check
command -v lab-check
lab-check
```

---

# Step 7: Documentation check

Use documentation to answer:

```bash
man bash
man chmod
man find
man wc
help printf
```

Do not read all of each manual page. Find one useful thing in each.

Fill in:

```text
man bash taught me:
man chmod taught me:
man find taught me:
man wc taught me:
help printf taught me:
```

---

# Final concept questions

Answer without notes:

1. What is a shell script?
2. What does the shebang do?
3. Why use `#!/usr/bin/env bash`?
4. Why test with `bash scriptname` before `chmod +x`?
5. What does executable permission allow?
6. Why does `./scriptname` differ from `scriptname`?
7. What is `PATH`?
8. Why use comments?
9. Why use long options in beginner scripts?
10. Why is line continuation useful?
11. Why should scripts be tested after small changes?
12. Why should he use `man`, `--help`, `type`, and `command -v`?

---

# Feynman final explanation

Explain Chapter 24 to a younger student in 10 sentences or fewer.

Required words:

```text
script
shell
shebang
permission
PATH
comments
verify
```

If he cannot explain it simply, he should reread the weak section and redo that exercise.

---

# End standard

He understands Chapter 24 only if he can say:

```text
I can turn a few useful commands into a script.
I know which shell runs the script.
I can make the script executable.
I know where the script lives.
I can verify which script is being run.
I can use documentation when I do not remember an option.
I can explain every line before I trust the script.
```
