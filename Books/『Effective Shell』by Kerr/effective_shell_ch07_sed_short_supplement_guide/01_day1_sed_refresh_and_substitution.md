# Day 1 — `sed` Refresh and Substitution

## Read before exercises

Read Kerr Ch. 7 from the start through these sections:

```text
Introducing the Stream Editor
Transformations with Sed
Replacing Text
Applying Multiple Expressions
Replacing Text on Specific Lines
```

## What he should gain

He should gain this practical ability:

```text
Use sed to transform text without editing the original file first.
```

He should also understand this important safety idea:

```text
sed usually prints changed output to stdout.
That means I can preview transformations before changing files.
```

Feynman analogy:

```text
sed is like a careful editor reading a document one line at a time.
For each line, it asks: “Does my rule apply here?”
If yes, it changes the line and sends the result forward.
```

---

# Before reading: memory refresh from TLCL

Answer before opening Kerr:

1. What does stdout mean?
2. What does redirection with `>` do?
3. What does a regex pattern describe?
4. In `grep`, does the pattern usually select words or whole lines?
5. In `sed 's/old/new/'`, what do the three `/` characters separate?

If he cannot answer these, briefly review TLCL Ch. 6, Ch. 19, and Ch. 20 notes.

---

# After reading: concept questions

Answer without looking back:

1. What does `sed` stand for?
2. Why is `sed` called a stream editor?
3. Does `sed 's/a/b/' file` usually change `file` directly?
4. In `s/settings/configuration/`, what is the search pattern?
5. What is the replacement text?
6. What is the difference between `s/old/new/` and `s/old/new/g`?
7. Why can a broad pattern accidentally change comments or unrelated text?
8. How can a line address such as `/^cp/` make a substitution safer?

---

# Setup

Create a small practice directory:

```bash
mkdir -p ~/effective-shell-ch07-sed
cd ~/effective-shell-ch07-sed
```

Create a sample script:

```bash
cat > backup-config.sh <<'EOF'
#!/usr/bin/env bash

# Copy over aws, gcp, ssh config and credentials.
mkdir -p ~/backup/settings/aws
mkdir -p ~/backup/settings/gcloud
mkdir -p ~/backup/settings/ssh
cp ~/.aws/config ~/backup/settings/aws/
cp ~/.aws/credentials ~/backup/settings/aws/
cp ~/.config/gcloud/credentials.db ~/backup/settings/gcloud/
cp ~/.ssh/config ~/backup/settings/ssh/
cp ~/.ssh/id_rsa.pub ~/backup/settings/ssh/
EOF
```

Inspect before transforming:

```bash
cat backup-config.sh
```

---

# Exercise 1: Simple substitution

## Discipline gate: do not type yet

Write first:

```text
Input file:
Transformation in English:
Expected changed lines:
Expected unchanged lines:
Risk:
```

For this exercise:

```text
Transformation in English:
Replace the text settings with configuration.
```

## Run

```bash
sed 's/settings/configuration/' backup-config.sh
```

## Inspect

Answer:

1. Did the original file change?
2. Which lines changed?
3. Did only the lines you expected change?
4. If you wanted to save the output, where should it go?

Try:

```bash
sed 's/settings/configuration/' backup-config.sh > changed.sh
cat changed.sh
cat backup-config.sh
```

Explain why `backup-config.sh` and `changed.sh` differ.

---

# Exercise 2: First-match vs global substitution

Create a line with repeated text:

```bash
printf '%s\n' 'settings settings settings' > repeated.txt
```

Predict before running:

```text
What will change with no g flag?
What will change with the g flag?
```

Run:

```bash
sed 's/settings/configuration/' repeated.txt
sed 's/settings/configuration/g' repeated.txt
```

Explain:

```text
Without g:
With g:
```

---

# Exercise 3: Accidental replacement

## Goal

Learn why a pattern must be precise.

Predict:

```text
What could go wrong if I replace cp everywhere?
```

Run:

```bash
sed 's/cp/rmdir/' backup-config.sh
```

Inspect the comment line carefully.

Answer:

1. Did `gcp` get changed?
2. Why is that a false positive?
3. What did the regex actually match?
4. What line type did we really want to change?

---

# Exercise 4: Replace only on specific lines

## Read before exercise

Reread the part about applying a substitution only to matching lines.

## Predict

Explain this command before running it:

```bash
sed '/^cp/s/cp/rmdir/' backup-config.sh
```

Answer:

```text
What does /^cp/ select?
What does s/cp/rmdir/ do?
Why is this safer than s/cp/rmdir/ alone?
```

## Run

```bash
sed '/^cp/s/cp/rmdir/' backup-config.sh
```

## Explain-back

In plain English:

```text
For every line, sed first checks whether the line starts with cp.
Only those lines receive the substitution.
```

---

# Day 1 finish standard

He is done only if he can explain:

```text
sed reads text and prints transformed text.
Substitution has the form s/pattern/replacement/.
The g flag changes all matches on a line.
A broad pattern causes false positives.
A line address limits where the command applies.
Previewing to stdout is safer than editing a file directly.
```
