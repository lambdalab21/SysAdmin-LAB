# Effective Shell Chapter 4: Regular Expression Essentials

This guide is a follow-up to Shotts Chapter 19, **Regular Expressions**.

Main purpose:

```text
Shotts Chapter 19 introduced regex symbols.
Kerr Chapter 4 should teach disciplined regex construction.
```

Core rule:

```text
Do not start with a complicated regex.
Start with examples, write the English rule, then build the regex one piece at a time.
```

Disciplined regex workflow:

```text
1. Write valid examples.
2. Write invalid or suspicious examples.
3. Write the English rule.
4. Build the smallest regex that matches something useful.
5. Test for false positives.
6. Test for false negatives.
7. Add one feature only when needed.
8. Explain every symbol.
```

One-time lab setup:

```bash
mkdir -p ~/effective-shell-ch04-regex/{session1,session2,session3,session4,session5,session6}
cd ~/effective-shell-ch04-regex

cat > email-addresses.txt <<'EOF'
dave@effective-shell.com
dave@effective-shell
to: dave@effective-shell.com
dave@effective-shell.com <Dave Kerr>
test123.effective-shell.com
@yahoo.com
dave@
whatever123@.com
dave@kerr@effective.shell.com
admin@example.org
root@localhost
first.last@example.co.uk
bad address@example.com
EOF

cat > log-lines.txt <<'EOF'
INFO 2026-06-01 app01 started
ERROR 2026-06-01 app01 failed-login alice
WARN 2026-06-01 db01 disk-space-low
ERROR 2026-06-02 db01 timeout
DEBUG 2026-06-03 app02 cache-warmed
error 2026-06-03 app02 lowercase-error-example
EOF

cat > html-snippets.txt <<'EOF'
This text is <strong>bold</strong>.
<a href="/index.html">Home</a>
<p>one</p><p>two</p>
no tags here
EOF

cat > config-sample.conf <<'EOF'
#Port 22
PermitRootLogin no
PasswordAuthentication yes
#PasswordAuthentication no
AllowUsers alice bob deploy
EOF
```

Before each session:

```bash
cd ~/effective-shell-ch04-regex
```

---
# Session 1: Introduction and Building from Examples

## What to read before exercises

Read these Chapter 4 sections:

```text
Regex Essentials — opening
An Introduction to Regular Expressions
Managing Complexity with Regular Expressions
Building Regexes — Start with the Basics
```

## What he should gain from this reading

He should gain this idea:

```text
Regex is not a secret code to memorize.
Regex is a rule for matching text.
The disciplined method is to start simple and refine.
```

He should also notice Kerr’s warning: complex regexes become hard to reason about. The solution is not “be clever.” The solution is to build the pattern in small steps.

---

## Feynman analogy

Imagine sorting index cards into two piles:

```text
Pile A: looks like an email address
Pile B: does not look like an email address
```

A regex is the rule you give to the sorter.

A bad rule:

```text
Find all valid email addresses perfectly.
```

A better beginner rule:

```text
Find lines that contain something, then @, then something.
```

That becomes:

```text
.*@.*
```

This is not perfect. It is a starting point.

---

# After reading: concept questions

Answer without typing commands:

1. Why do many people find regex intimidating?
2. Why should a regex start simple?
3. What is a false positive?
4. What is a false negative?
5. Why is an example list important before writing a regex?
6. Why is a giant internet regex often a bad learning tool?

---

# Exercise 1: Build examples first

Copy practice data into this session directory:

```bash
cp email-addresses.txt session1/
cd session1
cat email-addresses.txt
```

Before writing any regex, label each line:

```text
definitely valid:
definitely invalid:
uncertain / depends on rule:
```

Write at least three valid examples and three invalid examples.

---

# Exercise 2: Start with the simplest useful pattern

## Predict before typing

For this pattern:

```text
.*@.*
```

Answer:

1. What does the first `.*` mean?
2. What does `@` mean?
3. What does the second `.*` mean?
4. Which bad lines might match anyway?

## Run

Use `grep -E` for this guide:

```bash
grep -E '.*@.*' email-addresses.txt
```

## Explain-back

Explain each symbol:

```text
. means:
* means:
@ means:
```

---

# Exercise 3: False-positive notebook

Create a small table in your notes:

| Regex | What it should match | False positives | False negatives |
|---|---|---|---|
| `.*@.*` | lines containing @ | | |

Fill it out after running the command.

---

# Exercise 4: Do not improve too fast

He may want to jump to a complicated regex. Do not allow that yet.

Write this sentence:

```text
My current regex is imperfect, but I know exactly what it does.
```

That is better than using a regex he cannot explain.

---

# Documentation habit

Run:

```bash
grep --help | head
man grep
```

In `man grep`, search for extended regular expressions:

```text
/extended
```

Answer:

1. What option enables extended regular expressions?
2. Why are we using `grep -E` in this guide?
3. Does `man grep` explain every regex feature, or should he also check regex documentation?

---

# End standard

He is done with Session 1 only if he can say:

```text
I can build a regex from examples.
I know my first regex is not perfect.
I can explain why it matches too much.
I understand that regex improvement should be iterative.
```
