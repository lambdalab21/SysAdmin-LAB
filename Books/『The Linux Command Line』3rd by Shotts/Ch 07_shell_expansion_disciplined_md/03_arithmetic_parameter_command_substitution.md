#Book/The-Linux-Command-Line #Author/Shotts 
#redirection
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
# Session 3: Arithmetic, Parameter Expansion, and Command Substitution

## Read these Chapter 7 sections

Read the sections covering:

```text
arithmetic expansion
parameter expansion
command substitution
```

Focus on what happens before the final command runs.

---

## Feynman analogy

The shell can replace parts of your command with answers before the command runs.

```text
$USER        → your username
$HOME        → your home directory
$((2 + 3))   → 5
$(hostname)  → the output of hostname
```

The shell is doing pre-work.

---

# Part A: Parameter expansion

## Predict first

```bash
echo $USER
echo $HOME
echo $SHELL
echo $PWD
```

Run:

```bash
echo $USER
echo $HOME
echo $SHELL
echo $PWD
```

Use clearer boundaries:

```bash
printf '<%s>\n' "$USER"
printf '<%s>\n' "$HOME"
printf '<%s>\n' "$PWD"
```

Explain:

```text
$NAME asks the shell to replace the variable reference with the variable's value.
```

---

# Part B: Undefined variables

Predict:

```bash
echo $NOT_A_REAL_VARIABLE
printf '<%s>\n' "$NOT_A_REAL_VARIABLE"
```

Run:

```bash
echo $NOT_A_REAL_VARIABLE
printf '<%s>\n' "$NOT_A_REAL_VARIABLE"
```

Question:

```text
Why is printf with markers clearer than echo here?
```

Expected idea:

```text
It shows the empty value between markers.
```

---

# Part C: Arithmetic expansion

Predict:

```bash
echo $((2 + 3))
echo $((10 / 3))
echo $((10 % 3))
echo $((2 ** 8))
```

Run:

```bash
echo $((2 + 3))
echo $((10 / 3))
echo $((10 % 3))
echo $((2 ** 8))
```

Explain:

```text
$(( ... )) asks the shell to do integer arithmetic before running echo.
```

Question:

```text
Why does 10 / 3 not show 3.333?
```

Expected idea:

```text
Shell arithmetic is integer arithmetic here.
```

---

# Part D: Command substitution

Predict:

```bash
echo "This machine is $(hostname)"
echo "Today is $(date +%F)"
echo "There are $(ls docs/*.txt | wc -l) txt files in docs."
```

Run:

```bash
echo "This machine is $(hostname)"
echo "Today is $(date +%F)"
echo "There are $(ls docs/*.txt | wc -l) txt files in docs."
```

Explain:

```text
$(command) runs command first, captures its output, and inserts that output into the outer command.
```

---

## Discipline drill: hidden command awareness

For this command:

```bash
echo "There are $(find docs -type f -name '*.txt' | wc -l) text files."
```

Fill out before running:

```text
Outer command:
Inner command:
What the inner command outputs:
What the shell inserts:
What the final echo receives:
```

Then run it.

---

## Quoting preview

Compare:

```bash
echo $HOME
echo "$HOME"
echo '$HOME'
```

Do not worry if the quoting rules are not fully clear yet. Session 4 focuses on quoting.

---

## Documentation habit

Run:

```bash
man bash
```

Search for:

```text
/Parameter Expansion
/Arithmetic Expansion
/Command Substitution
```

Then use shell built-in help:

```bash
help let
help printf
```

Write:

```text
Which expansions are documented in man bash rather than man echo?
```

Expected idea:

```text
The shell performs expansions, so they are documented under bash, not under each external command.
```

---

## Checkpoint

He understands Session 3 only if he can explain:

1. What `$USER` does.
2. What happens when a variable is undefined.
3. What `$((2 + 3))` does.
4. What `$(hostname)` does.
5. Why command substitution can hide a command inside another command.
