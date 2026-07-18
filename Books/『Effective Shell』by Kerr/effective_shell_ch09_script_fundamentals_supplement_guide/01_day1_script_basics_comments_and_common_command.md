# Day 1: Script Basics, Comments, and the `common` Command

## Read before exercises

Read Effective Shell Ch. 9 from the beginning through these sections:

```text
What is a Shell Script?
Creating a Basic Shell Script
The 'common' Command
Creating a Simple Script
Comments
Building and Testing the Script
Multi-line Commands
```

## What he should gain

He should refresh this idea:

```text
A script is a saved workflow.
If I repeat a command sequence, I can save it, name it, test it, and improve it.
```

He should also gain this practical technique:

```text
Build a pipeline interactively first.
Then save it in a script.
Then make it readable.
```

## Feynman analogy

A script is like a recipe.

```text
Bad recipe:
Do things, then more things, then some more things.

Good recipe:
Name the dish.
Explain why each major step exists.
Keep the steps in a reusable order.
```

A good shell script is a recipe for the computer.

---

# After reading: concept questions

Answer without looking back:

1. What is a shell script?
2. Why would you save commands in a script instead of retyping them?
3. What is the purpose of a comment?
4. Why is a comment explaining “why” usually better than a comment explaining only “what”?
5. What does the `common` script try to show?
6. Why does the chapter build the command from shell history?
7. Why is `sort` used before `uniq -c`?
8. Why is numeric reverse sort useful near the end of the pipeline?
9. What does the backslash `\` do at the end of a line in a multi-line command?
10. What mistake breaks a backslash line continuation?

Do not do the exercises until these are answered.

---

# Exercise 1: Explain the pipeline before typing it

## Skill being gained

He is refreshing TLCL Ch. 19–20 skills: text streams, pipelines, sorting, counting, `sed`, and `head`.

## Do not type yet

First explain this pipeline in English:

```bash
tail ~/.bash_history -n 1000 | sort | uniq -c | sed 's/^ *//' | sort -n -r | head -n 10
```

Fill this table:

| Stage | Command | What enters | What leaves | Why this stage exists |
|---|---|---|---|---|
| 1 | `tail ~/.bash_history -n 1000` | history file | recent commands | |
| 2 | `sort` | recent commands | grouped duplicates | |
| 3 | `uniq -c` | sorted commands | counts plus command text | |
| 4 | `sed 's/^ *//'` | counts with leading spaces | cleaner counts | |
| 5 | `sort -n -r` | counted lines | highest count first | |
| 6 | `head -n 10` | sorted counted lines | top ten | |

## Prediction

Before running, answer:

```text
What commands do I expect to appear in my top ten?
Could aliases appear?
Could repeated mistakes appear?
Could private or embarrassing commands appear?
```

This matters because shell history can contain sensitive information.

## Run the pipeline first, not the script

```bash
tail ~/.bash_history -n 1000 | sort | uniq -c | sed 's/^ *//' | sort -n -r | head -n 10
```

If `~/.bash_history` is missing or empty, use this temporary practice file:

```bash
mkdir -p ~/effective-shell-ch09-practice
cat > ~/effective-shell-ch09-practice/history-sample.txt <<'EOF'
ls
cd projects
git status
ls
git status
vim notes.md
ls
git status
cd projects
EOF

tail ~/effective-shell-ch09-practice/history-sample.txt -n 1000 | sort | uniq -c | sed 's/^ *//' | sort -n -r | head -n 10
```

## Explain-back

Answer:

1. Why did we test the pipeline before putting it in a script?
2. Which stage depends on duplicates being adjacent?
3. What would break if `sort` before `uniq -c` were removed?
4. Why is this a good review of TLCL text processing?

---

# Exercise 2: Create the first script version

## Skill being gained

He is refreshing TLCL Ch. 24: create a script, run it explicitly, and inspect output.

## Read before exercise

Reread:

```text
Creating a Simple Script
Comments
Building and Testing the Script
```

## Before typing

Write:

```text
Script name:
Job of script:
Why this script is useful:
Commands I expect to use:
How I will test it:
```

## Create the script

```bash
mkdir -p ~/scripts
cat > ~/scripts/common.v1.sh <<'EOF'
# common.v1.sh - show the most common recent shell commands

# Print the report title.
echo "common commands:"

# Count repeated recent shell commands and show the top ten.
tail ~/.bash_history -n 1000 | sort | uniq -c | sed 's/^ *//' | sort -n -r | head -n 10
EOF
```

Run it explicitly:

```bash
sh ~/scripts/common.v1.sh
```

Then try:

```bash
bash ~/scripts/common.v1.sh
```

## Explain-back

Answer:

1. Did `sh` and `bash` both work here?
2. Why might relying on `sh` be risky in scripts that use Bash-specific features?
3. Which comments explain “why”?
4. Which comments merely describe “what”?
5. How could the comments be improved?

---

# Exercise 3: Make the long pipeline readable

## Skill being gained

He is learning readability without changing behavior.

## Read before exercise

Reread:

```text
Multi-line Commands
```

## Prediction

Before editing, answer:

```text
If I split a pipeline across lines with backslashes, should the output change?
What must be true about the backslash character?
```

## Rewrite the script

```bash
cat > ~/scripts/common.v1.sh <<'EOF'
# common.v1.sh - show the most common recent shell commands

# Print the report title.
echo "common commands:"

# Count repeated recent shell commands and show the top ten.
tail ~/.bash_history -n 1000 \
    | sort \
    | uniq -c \
    | sed 's/^ *//' \
    | sort -n -r \
    | head -n 10
EOF
```

Run:

```bash
bash ~/scripts/common.v1.sh
```

## Deliberate breakage

Copy it:

```bash
cp ~/scripts/common.v1.sh ~/scripts/common-broken.sh
```

Edit `common-broken.sh` and put a space or comment after one backslash, such as:

```bash
    | sort \  # bad
```

Run:

```bash
bash ~/scripts/common-broken.sh
```

Then delete it:

```bash
rm -f ~/scripts/common-broken.sh
```

## Explain-back

Answer:

1. Why did the correct multi-line version work?
2. Why did the broken version fail?
3. Why is this more readable than one very long line?
4. Why is one command per pipeline stage easier to debug?

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
I can build a useful pipeline first.
I can save it into a script.
I can write comments that explain intent.
I can split a pipeline across lines safely.
I can explain every command in the script.
```
