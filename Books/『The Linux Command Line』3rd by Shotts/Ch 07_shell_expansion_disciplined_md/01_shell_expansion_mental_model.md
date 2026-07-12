#Book/The-Linux-Command-Line Author/Shotts 
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
# Session 1: The Shell Expansion Mental Model

## Read these Chapter 7 sections

Read the beginning of Chapter 7 where Author/Shotts explains expansion and why the shell sees commands differently from humans.

Focus on:

```text
expansion
arguments
what the shell does before the command runs
using echo to preview expansion
```

---

## Before touching the keyboard

Answer:

1. Does `ls *.txt` send `*.txt` to `ls`, or does the shell expand it first? No. The shell expands it first into matching filenames.  
2. What command can preview expansion? `echo` or `print`. 
3. Why is expansion dangerous with `rm`? The shell expands wildcards before `rm` runs, potentially deleting many files unexpectedly. 
4. Why is Chapter 7 important before shell scripting? Understanding how the shell modifies commands is essential for writing correct scripts. 

---

## Practice setup

```bash
cd ~/tlcl-ch07-lab
```

Run:

```bash
echo *.txt
echo docs/*.txt
printf '<%s>\n' docs/*.txt
```

---

## Dangerous contrast

Preview only:

```bash
echo rm tmp/*.tmp
printf '<%s>\n' tmp/*.tmp
```

Do **not** run:

```bash
rm tmp/*.tmp
```

Question:

```text
Why is echo rm tmp/*.tmp useful but not a perfect safety guarantee? It shows the expected command idea, but files can change between preview and execution.
```

---

## Documentation habit

Use documentation for the shell, not only external commands:

```bash
help echo
man bash
```

Inside `man bash`, search for:

```text
/EXPANSION
/Pathname Expansion
/Quoting
```

Write one sentence:

```text
Where does Bash document expansion? In the man bash page under sections like EXPANSION, pathname expansion, and quoting. 
```

---

## Checkpoint

He understands Session 1 only if he can explain:

1. What expansion means. The shell rewrites your command before the program receives it. 
2. Why `echo *.txt` is a preview tool. It shows the expanded list without running a dangerous command. 
3. Why `printf '<%s>\n' *.txt` can be clearer than `echo`. It displays each argument on its own line, making spaces and separate items obvious. 
4. Why destructive commands with wildcards are risky. The shell expands them first. One typo can delete more than intended. 
5. Why the shell, not the program, often handles wildcards. Pathname extensions are performed by the shell before passing arguments to the command. 