#Book/The-Linux-Command-Line  #Author/Shotts 
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
# Session 4: Quoting and Escaping

## Read these Chapter 7 sections

Read the sections covering:

```text
double quotes
single quotes
escaping characters
backslash
```

This is the most important safety session in Chapter 7.

---

## Feynman analogy

Quoting tells the shell how much freedom it has to rewrite your command.

```text
no quotes     = shell has lots of freedom
"double"      = shell still expands variables and command substitutions, but protects spaces/globs
'single'      = shell treats almost everything literally
\ character   = protect one character
```

Think of quoting as putting a fence around text.

---

# Part A: Spaces in filenames

Predict:

```bash
ls docs/file with spaces.txt
ls "docs/file with spaces.txt"
```

Run:

```bash
ls docs/file with spaces.txt
ls "docs/file with spaces.txt"
```

Question:

```text
Why did the unquoted version fail or behave differently?
```

Expected idea:

```text
The shell split the unquoted words into separate arguments.
```

Use printf to see argument splitting:

```bash
printf '<%s>\n' docs/file with spaces.txt
printf '<%s>\n' "docs/file with spaces.txt"
```

---

# Part B: Double quotes

Predict:

```bash
echo "$HOME"
echo "My home is $HOME"
echo "Today is $(date +%F)"
echo "docs/*.txt"
```

Run:

```bash
echo "$HOME"
echo "My home is $HOME"
echo "Today is $(date +%F)"
echo "docs/*.txt"
```

Explain:

```text
Double quotes preserve spaces and prevent pathname expansion, but variable expansion and command substitution still happen.
```

---

# Part C: Single quotes

Predict:

```bash
echo '$HOME'
echo 'Today is $(date +%F)'
echo 'docs/*.txt'
```

Run:

```bash
echo '$HOME'
echo 'Today is $(date +%F)'
echo 'docs/*.txt'
```

Explain:

```text
Single quotes preserve literal text. Most expansions do not happen inside single quotes.
```

---

# Part D: Backslash escaping

Predict:

```bash
echo \$HOME
echo docs/important\[final\].txt
ls docs/important\[final\].txt
```

Run:

```bash
echo \$HOME
echo docs/important\[final\].txt
ls docs/important\[final\].txt
```

Explain:

```text
Backslash protects the next character from special shell meaning.
```

---

## Discipline drill: choose the right quote

For each goal, choose no quote, double quote, single quote, or backslash.

| Goal | Best choice | Why? |
|---|---|---|
| Show the value of `$HOME` | | |
| Print the literal text `$HOME` | | |
| Use a filename with spaces | | |
| Prevent `*.txt` from expanding | | |
| Run `$(hostname)` inside a sentence | | |
| Print literal `$(hostname)` | | |

Expected ideas:

```text
Show variable: double quotes are usually safe.
Literal variable: single quotes or backslash.
Filename with spaces: double quotes.
Prevent glob: quotes.
Command substitution in sentence: double quotes.
Literal command substitution: single quotes.
```

---

## Safety drill

Do not run the `rm` command. Preview only.

```bash
echo rm docs/*.txt
echo rm "docs/*.txt"
echo rm 'docs/*.txt'
```

Question:

```text
Which one expands to real files?
Which one passes the literal pattern?
Which one would be dangerous if run accidentally?
```

---

## Documentation habit

Run:

```bash
man bash
```

Search for:

```text
/QUOTING
/Double Quotes
/Single Quotes
/Escape Character
```

Also try:

```bash
help printf
printf --help 2>/dev/null || true
```

Note:

```text
Some commands are shell builtins. For builtins, help may be more useful than man.
```

---

## Checkpoint

He understands Session 4 only if he can answer:

1. What do double quotes still allow?
2. What do single quotes prevent?
3. Why do spaces break unquoted filenames?
4. What does backslash protect?
5. Why should variables usually be quoted in scripts later?
