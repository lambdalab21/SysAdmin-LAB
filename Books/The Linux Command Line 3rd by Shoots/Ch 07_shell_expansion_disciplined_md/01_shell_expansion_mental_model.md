#Book/The-Linux-Command-Line  Author/Shotts 
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

## Feynman analogy

Imagine you write a note to a helper:

```text
Show me *.txt
```

But before the helper receives the note, a secretary rewrites it:

```text
Show me docs/report.txt docs/todo.txt docs/file with spaces.txt
```

The helper never saw `*.txt`. The helper saw the expanded list.

That secretary is the shell.

```text
you type text
→ shell expands it
→ program receives arguments
```

---

## Before touching the keyboard

Answer:

1. Does `ls *.txt` send `*.txt` to `ls`, or does the shell expand it first?
2. What command can preview expansion?
3. Why is expansion dangerous with `rm`?
4. Why is Chapter 7 important before shell scripting?

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

## Explain-back

Say aloud:

```text
The shell expands docs/*.txt into matching pathnames before echo or printf runs.
printf shows each resulting argument clearly.
```

---

## Discipline drill: command-receipt thinking

For each command, fill this out before running:

```text
Typed command:
What the shell expands:
What the program receives:
Is it safe?
```

Commands:

```bash
echo logs/*.log
printf '<%s>\n' logs/*.log
ls logs/*.log
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
Why is echo rm tmp/*.tmp useful but not a perfect safety guarantee?
```

Expected idea:

```text
It shows the expanded command idea, but files can change between preview and execution. Still, previewing is much safer than guessing.
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
Where does Bash document expansion?
```

---

## Checkpoint

He understands Session 1 only if he can explain:

1. What expansion means.
2. Why `echo *.txt` is a preview tool.
3. Why `printf '<%s>\n' *.txt` can be clearer than `echo`.
4. Why destructive commands with wildcards are risky.
5. Why the shell, not the program, often handles wildcards.
