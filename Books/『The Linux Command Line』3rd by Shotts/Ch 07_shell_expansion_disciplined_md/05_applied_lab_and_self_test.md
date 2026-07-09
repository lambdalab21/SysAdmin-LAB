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

# Lab 1: Expansion receipt

For each command, identify what the final program receives.

```bash
printf '<%s>\n' docs/*.txt
printf '<%s>\n' "docs/*.txt"
printf '<%s>\n' 'docs/*.txt'
```

Fill out:

```text
Command: `printf '<%s>\n' docs/*.txt`
Expanded or literal? Expanded (pathname/globbing expansion)
How many arguments? five
Why? The shell expanded docs/*.txt to match .txt files in docs/. printf recieved each filename as a separate argument. 
```

---

# Lab 2: Brace expansion versus pathname expansion

Run:

```bash
echo photos/img{001,002,010}.jpg
echo photos/img00?.jpg
echo photos/img{003,004}.jpg
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
Why is rm -i safer?rm -i prompts before deleting. 
Why is tmp/*.tmp safer than *.tmp from the wrong directory? tmp/*.tmp limits to correct directory. 
What could still go wrong? Forgetting cd, unquoted spaces, or * matching too much.
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
Double quotes allow variable expansion and command substitution. 
Single quotes prevented all explanations. 
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
How many arguments did printf receive in each case? Three arguments for unquoted, one argument for quoted. 
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

---

# Lab 7: Documentation scavenger hunt

Use documentation to answer these.

1. Where does Bash document pathname expansion? man bash. 
2. Where does Bash document quoting? man bash. 
3. What does `help printf` show? It shows format specifiers, escapes, and options. 
4. Is `echo` always reliable for precise output? Why might `printf` be better? No. Echo adds spaces/newlines and has quirks where printf gives precise control. 
5. Which documentation source helped more for expansion: `man ls` or `man bash`? man bash. 

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

1. What does pathname expansion do? * ? [ ] matches files. 
2. What does brace expansion do? generates combinations. 
3. What does tilde expansion do? home directory. 
4. What does parameter expansion do? variable value. 
5. What does command substitution do? output of command. 
6. What do double quotes allow? allows $, ', and \.
7. What do single quotes prevent? Prevents all expansions. 
8. Why is `printf '<%s>\n' PATTERN` useful? Shows exact arguments after expansion. 
9. Why is `rm *.tmp` dangerous? Can delete wrong files if not in the right directory or unquoted. 
10. Why should shell expansion be understood before scripting? Expansions happen before commands and scripts run. 

