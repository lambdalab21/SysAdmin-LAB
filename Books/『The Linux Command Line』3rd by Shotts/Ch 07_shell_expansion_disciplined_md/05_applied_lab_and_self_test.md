#Book/The-Linux-Command-Line #Author/Shotts 
#expansion #shell-expansion 
# The Linux Command Line, Chapter 7: Seeing the World as the Shell Sees It

Use these guides after Chapter 6 redirection/pipelines and before heavy scripting.

Main idea:

```text
The program does not always receive what you typed.
The shell often expands, splits, or removes characters first.
```

Disciplined habit:

```text
Before pressing Enter, ask:
1. What will the shell change before the command runs?
2. Will wildcards expand?
3. Will variables expand?
4. Will command substitution run another command first?
5. Do I need quotes?
6. Is this command destructive?
7. Can I preview with echo or printf first?
```

Safer preview tools:

```bash
echo PATTERN
printf '<%s>
' PATTERN
```

Use `printf '<%s>
' ...` when you want to see each resulting argument clearly.

One-time setup:

```bash
mkdir -p ~/tlcl-ch07-lab/{docs,logs,photos,backup,tmp}
cd ~/tlcl-ch07-lab

touch docs/report.txt docs/report-old.txt docs/notes.md docs/todo.txt
touch logs/app.log logs/error.log logs/access.log
touch photos/img001.jpg photos/img002.jpg photos/img010.jpg
touch tmp/file1.tmp tmp/file2.tmp tmp/file-with-space.tmp
mkdir -p backup/2026 backup/old

touch "docs/file with spaces.txt"
touch "docs/important[final].txt"
```

Verify:

```bash
find ~/tlcl-ch07-lab -maxdepth 3 -print | sort
```

---
# Session 5: Applied Lab and Self-Test

## Purpose

This lab checks whether he understands Chapter 7 as a thinking skill.

He must not type commands mindlessly.

For each task:

```text
1. Predict expansion.
2. Preview with echo or printf.
3. Run the harmless command.
4. Explain what happened.
5. Identify the risk if the command were destructive.
```

---

# Lab 1: Expansion receipt

For each command, identify what the final program receives.

```bash
printf '<%s>\n' docs/*.txt
printf '<%s>\n' "docs/*.txt"
printf '<%s>\n' 'docs/*.txt'
```

Fill out:

```text
Command:
Expanded or literal?
How many arguments?
Why?
```

---

# Lab 2: Brace expansion versus pathname expansion

Predict first:

```bash
echo photos/img{001,002,010}.jpg
echo photos/img00?.jpg
echo photos/img{003,004}.jpg
```

Run:

```bash
echo photos/img{001,002,010}.jpg
echo photos/img00?.jpg
echo photos/img{003,004}.jpg
```

Explain:

```text
Which command checked existing files?
Which command merely manufactured text?
```

Expected idea:

```text
Pathname expansion checks existing matching files.
Brace expansion manufactures text and does not care whether files exist.
```

---

# Lab 3: Build a safe cleanup command

Goal:

```text
Remove only tmp files inside ~/tlcl-ch07-lab/tmp.
```

Step 1: Confirm directory.

```bash
cd ~/tlcl-ch07-lab
pwd
```

Step 2: Preview expansion.

```bash
printf '<%s>\n' tmp/*.tmp
```

Step 3: Safer interactive command.

```bash
echo rm -i tmp/*.tmp
```

Optional, only if this is the practice lab and he has previewed correctly:

```bash
rm -i tmp/*.tmp
```

Recreate files if needed:

```bash
touch tmp/file1.tmp tmp/file2.tmp tmp/file-with-space.tmp
```

Explain:

```text
Why is rm -i safer?
Why is tmp/*.tmp safer than *.tmp from the wrong directory?
What could still go wrong?
```

---

# Lab 4: Variable and command substitution

Predict:

```bash
echo "User: $USER"
echo 'User: $USER'
echo "Host: $(hostname)"
echo 'Host: $(hostname)'
```

Run:

```bash
echo "User: $USER"
echo 'User: $USER'
echo "Host: $(hostname)"
echo 'Host: $(hostname)'
```

Explain:

```text
Double quotes allowed...
Single quotes prevented...
```

---

# Lab 5: Argument-count thinking

Run:

```bash
printf 'Argument: <%s>\n' docs/file with spaces.txt
printf 'Argument: <%s>\n' "docs/file with spaces.txt"
```

Question:

```text
How many arguments did printf receive in each case?
```

Expected idea:

```text
The unquoted version split at spaces. The quoted version preserved one pathname as one argument.
```

---

# Lab 6: Explain these commands without notes

For each, identify the expansions and quoting behavior.

```bash
echo ~
echo "$HOME"
echo '$HOME'
echo $((7 * 6))
echo "Today is $(date +%F)"
echo {a,b,c}-{1,2}
printf '<%s>\n' photos/img00?.jpg
printf '<%s>\n' "photos/img00?.jpg"
```

Use this form:

```text
Command:
Expansion type:
What shell produces:
What program receives:
Risk:
```

---

# Lab 7: Documentation scavenger hunt

Use documentation to answer these.

1. Where does Bash document pathname expansion?
2. Where does Bash document quoting?
3. What does `help printf` show?
4. Is `echo` always reliable for precise output? Why might `printf` be better?
5. Which documentation source helped more for expansion: `man ls` or `man bash`?

Commands:

```bash
man bash
help echo
help printf
man ls
```

Expected idea:

```text
Expansion is a shell feature, so man bash is the right place.
ls receives arguments after expansion; it does not perform shell expansion itself.
```

---

# Final self-test

Without notes, answer:

1. What does pathname expansion do?
2. What does brace expansion do?
3. What does tilde expansion do?
4. What does parameter expansion do?
5. What does command substitution do?
6. What do double quotes allow?
7. What do single quotes prevent?
8. Why is `printf '<%s>\n' PATTERN` useful?
9. Why is `rm *.tmp` dangerous?
10. Why should shell expansion be understood before scripting?

---

# End standard

He understands Chapter 7 only if he can say:

```text
I know the shell rewrites parts of my command before the program runs.
I can predict pathname, brace, tilde, variable, arithmetic, and command substitution.
I can choose between no quotes, double quotes, single quotes, and backslash.
I preview expansion before destructive commands.
I know to read man bash for shell expansion rules.
```
