#Book/The-Linux-Command-Line  #Author/Shotts 
#expansion #shell-expansion 
# The Linux Command Line, Chapter 7: Seeing the World as the Shell Sees It

Use these guides after Chapter 6 redirection/pipelines and before heavy scripting.

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
What did [12] mean?Matches 1 or 2 in that position. 
What did [!1] mean? Matches any character except for 1 in that position. 
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
## Safety habit

Never jump from this:

```bash
echo tmp/*.tmp
```

to this without thinking:

```bash
rm tmp/*.tmp
```

---
## Checkpoint

He understands Session 2 only if he can answer:

1. What does `*` do in pathname expansion? Matches any characters. 
2. What does `?` do? Matches exactly one character. 
3. What does `[12]` do? Matches any one character from the set inside brackets. 
4. What does `~` expand to? Home directory. 
5. Why can brace expansion produce names that do not exist? It generates text patterns before the command runs; it does not check for file existence. 