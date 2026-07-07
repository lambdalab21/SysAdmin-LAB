# The Linux Command Line, Chapter 7: Seeing the World as the Shell Sees It

Use these guides after Chapter 6 redirection/pipelines and before heavy scripting.

Main idea:

```text
The program does not always receive what you typed.
The shell often expands, splits, or removes characters first.
```

Safer preview tools:

```bash
echo PATTERN
printf '<%s>
' PATTERN
```

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
Why is printf with markers clearer than echo here? It showsx empty values using markers, making blank output easier to see. 
```
---

# Part C: Arithmetic expansion

Run:

```bash
echo $((2 + 3))
echo $((10 / 3))
echo $((10 % 3))
echo $((2 ** 8))
```

Question:

```text
Why does 10 / 3 not show 3.333? Because shell arithmetic uses integer math, so the decimal is discarded(like 10/3=3)
```

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
## Checkpoint

He understands Session 3 only if he can explain:

1. What `$USER` does replaces $user with the current username. 
2. What happens when a variable is undefined. It expands to an empty string.
3. What `$((2 + 3))` does. Evaluates the arithmetic expression and returns 5. 
4. What `$(hostname)` does. Runs the hostname command and substitutes its output. 
5. Why command substitution can hide a command inside another command. Because the shell executes the inner command first and inserts its output into the outer command. 
