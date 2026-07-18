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
# Session 2: Pathname, Tilde, and Brace Expansion

## Read these Chapter 7 sections

Read the sections covering:

```text
pathname expansion
wildcards
~ tilde expansion
brace expansion
```

Focus on prediction, not memorizing examples.

---

## Feynman analogy

Pathname expansion is like asking a librarian:

```text
Bring every book whose title ends in .log.
```

Tilde expansion is a shortcut address:

```text
~ means my home directory.
```

Brace expansion is like a stamp-maker:

```text
file-{a,b,c}.txt
```

becomes:

```text
file-a.txt file-b.txt file-c.txt
```

Important: brace expansion does **not** check whether files exist. It just manufactures text.

---

# Part A: Pathname expansion

## Predict first

Before running, write expected matches:

```bash
echo docs/*.txt
echo logs/*.log
echo photos/img00?.jpg
echo photos/img0*.jpg
```

Now run:

```bash
echo docs/*.txt
echo logs/*.log
echo photos/img00?.jpg
echo photos/img0*.jpg
```

Use clearer output:

```bash
printf '<%s>\n' docs/*.txt
printf '<%s>\n' photos/img00?.jpg
```

## Explain-back

```text
* matches any characters in a pathname component.
? matches one character.
The shell replaces the pattern with matching pathnames.
```

---

# Part B: Character classes

Predict:

```bash
echo photos/img00[12].jpg
echo photos/img00[!1].jpg
```

Run:

```bash
echo photos/img00[12].jpg
echo photos/img00[!1].jpg
```

Question:

```text
What did [12] mean?
What did [!1] mean?
```

---

# Part C: Tilde expansion

Predict:

```bash
echo ~
echo ~/tlcl-ch07-lab
```

Run:

```bash
echo ~
echo ~/tlcl-ch07-lab
cd ~
pwd
cd ~/tlcl-ch07-lab
```

Explain:

```text
~ expands to my home directory.
```

---

# Part D: Brace expansion

Predict before running:

```bash
echo file-{a,b,c}.txt
echo {1..5}
echo backup/{2024,2025,2026}
echo photos/img{001,002,010}.jpg
```

Run:

```bash
echo file-{a,b,c}.txt
echo {1..5}
echo backup/{2024,2025,2026}
echo photos/img{001,002,010}.jpg
```

Now prove that brace expansion does not require existing files:

```bash
echo does-not-exist-{a,b,c}.txt
ls does-not-exist-{a,b,c}.txt
```

The `echo` works because it only manufactures text. The `ls` fails because the files do not exist.

---

## Discipline drill: expansion type identification

For each command, identify the expansion type before running:

```bash
echo ~
echo *.md
echo logs/*.log
echo {alpha,beta,gamma}
echo file-{1..3}.txt
echo photos/img00?.jpg
```

Use this form:

```text
Command:
Expansion type:
Expected output:
Actual output:
What I learned:
```

---

## Safety habit

Never jump from this:

```bash
echo tmp/*.tmp
```

to this without thinking:

```bash
rm tmp/*.tmp
```

Required safety checklist:

```text
Am I in the intended directory?
Did the pattern expand to exactly the files I expected?
Could it match too much?
Should I use rm -i?
Should I move files to a temporary review directory instead?
```

---

## Documentation habit

Run:

```bash
man bash
```

Search for:

```text
/Pathname Expansion
/Brace Expansion
/Tilde Expansion
```

Write:

```text
One fact I learned from man bash:
One thing I still do not understand:
```

---

## Checkpoint

He understands Session 2 only if he can answer:

1. What does `*` do in pathname expansion?
2. What does `?` do?
3. What does `[12]` do?
4. What does `~` expand to?
5. Why can brace expansion produce names that do not exist?
