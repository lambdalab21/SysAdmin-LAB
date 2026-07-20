# Day 2 — Multiple Expressions, Comments, and Line Edges

## Read before exercises

Read these Kerr Ch. 7 sections:

```text
Applying Multiple Expressions
Stripping Comments
Appending Text
Prepending Text
```

## What he should gain

He should gain this ability:

```text
Build a sed transformation in small stages instead of writing a clever unreadable command at once.
```

He should also learn:

```text
^ means start of line.
$ means end of line.
Multiple sed expressions run in order.
The output of one expression becomes the input to the next expression.
```

Feynman analogy:

```text
A sed pipeline with multiple expressions is like an assembly line.
Station 1 removes comments.
Station 2 removes empty lines.
Station 3 adds text to the end.
The order matters because each station receives the changed product from the previous station.
```

---

# Before reading: memory refresh

Answer before reading:

1. What does `^` mean in regex?
2. What does `$` mean in regex?
3. What does `grep -v` do?
4. What is a blank line?
5. Why should you inspect each pipeline stage before adding another?

---

# After reading: concept questions

Answer without looking back:

1. What does `sed -e` allow you to do?
2. Why can expression order matter?
3. How can `sed` delete lines?
4. How can `sed` append text to the end of a line?
5. How can `sed` prepend text to the start of a line?
6. What does `sed -n '/pattern/p'` do?
7. Why is `-n` useful while building a command?
8. What danger appears when a command silently changes more lines than expected?

---

# Setup

Use the same directory:

```bash
cd ~/effective-shell-ch07-sed
```

Create a noisy config file:

```bash
cat > app.conf <<'EOF'
# Example application config

app_name = demo
port = 8080
# debug = true
host = localhost

# End of config
EOF
```

Inspect:

```bash
nl -ba app.conf
```

---

# Exercise 1: Print only matching lines

## Discipline gate

Before typing, answer:

```text
Which lines should print?
Which lines should not print?
Why am I using -n?
```

Run:

```bash
sed -n '/=/p' app.conf
```

Explain:

```text
-n means do not print automatically.
/p means print lines matching this address.
```

---

# Exercise 2: Delete comments

Predict before running:

```text
What lines start with #?
Will indented comments be deleted?
Will blank lines be deleted?
```

Run:

```bash
sed '/^#/d' app.conf
```

Answer:

1. What did `d` do?
2. Did blank lines remain?
3. Would `^[[:space:]]*#` be broader? Why?

Try the broader version:

```bash
sed '/^[[:space:]]*#/d' app.conf
```

---

# Exercise 3: Delete blank lines too

## Skill

Use multiple expressions in order.

Predict:

```text
Expression 1 deletes comments.
Expression 2 deletes blank lines.
What should remain?
```

Run:

```bash
sed -e '/^[[:space:]]*#/d' -e '/^[[:space:]]*$/d' app.conf
```

Explain each expression:

```text
/^[[:space:]]*#/d:
/^[[:space:]]*$/d:
```

---

# Exercise 4: Append text to line ends

Create a list:

```bash
cat > commands.txt <<'EOF'
cp file1 backup/
cp file2 backup/
cp file3 backup/
EOF
```

Predict:

```text
What does $ match?
What happens if I substitute the end of a line with ; ?
```

Run:

```bash
sed 's/$/;/' commands.txt
```

Explain:

```text
The pattern $ does not match a visible character.
It matches the position at the end of the line.
```

---

# Exercise 5: Prepend text to line starts

Predict:

```text
What does ^ match?
What will echo be added to?
```

Run:

```bash
sed 's/^/echo /' commands.txt
```

Explain:

```text
The pattern ^ matches the position at the start of the line.
```

---

# Exercise 6: Build in stages

Goal:

```text
Turn cp commands into safe preview commands.
```

Do not jump to the final command. Build stages.

Stage 1:

```bash
sed 's/$/"/' commands.txt
```

Stage 2:

```bash
sed -e 's/$/"/' -e 's/^/echo "/' commands.txt
```

Answer:

1. Why did we add the ending quote first?
2. Why does expression order matter?
3. What would happen if one expression changed text needed by the next expression?

---

# Day 2 finish standard

He is done only if he can explain:

```text
sed -e applies multiple commands.
Commands are applied in order.
/p prints selected lines when automatic printing is disabled with -n.
d deletes selected lines from output.
^ and $ can be used as positions, not just visible characters.
Complex sed commands should be built and inspected one stage at a time.
```
