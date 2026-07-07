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
# Session 3: Character Sets, Metacharacters, and Escaping

## What to read before exercises

Read these Chapter 4 sections:

```text
Building Regexes — Character Sets and Metacharacters
Character Sets — Ranges
Character Sets — Special Characters
Character Sets — Negating Characters
Character Sets — Escaping Characters
Character Sets — Quick Reference
```

## What he should gain from this reading

He should gain this idea:

```text
Do not use “any character” when you can say what characters are allowed.
```

A disciplined regex becomes clearer by replacing broad matches like `.` with more explicit character sets.

---

## Feynman analogy

Suppose you are a doorman at an event.

Bad rule:

```text
Let anyone in.
```

Better rule:

```text
Let in people with a student ID or staff ID.
```

In regex:

```text
.        = almost any character
[A-Z]    = uppercase letters
[0-9]    = digits
[^@]     = not an at sign
```

Character sets make the rule less sloppy.

---

# After reading: concept questions

Answer before typing:

1. What does `[A-Za-z0-9]` match?
2. Why does `[A-Za-z0-9]+@[A-Za-z0-9]+` fail for some realistic addresses?
3. Why is `.` sometimes too broad?
4. What does `[^@]` mean?
5. How do you match a literal `[` or `]`?
6. Why should he be cautious with shortcuts like `\w` and `\d` across tools?

---

# Exercise 1: Replace dot with a character set

```bash
cp email-addresses.txt session3/
cd session3
```

Start broad:

```bash
grep -E '.+@.+' email-addresses.txt
```

Now try a range:

```bash
grep -E '[A-Za-z0-9]+@[A-Za-z0-9]+' email-addresses.txt
```

Answer:

```text
What did the range version miss?
Why did it miss addresses with dots or hyphens?
```

---

# Exercise 2: Add allowed punctuation

Try:

```bash
grep -E '[A-Za-z0-9.-]+@[A-Za-z0-9.-]+' email-addresses.txt
```

Answer:

1. Which valid-looking lines now match?
2. Which bad lines still match?
3. Did adding characters fix everything?

---

# Exercise 3: “Not @” thinking

Try:

```bash
grep -E '[^@]+@[^@]+' email-addresses.txt
```

Explain:

```text
[^@]+ before @ means:
@ means:
[^@]+ after @ means:
```

Question:

```text
Does this prevent two @ signs in the whole line?
```

Hint: Test it against the existing examples.

---

# Exercise 4: Literal brackets

Create data:

```bash
cat > package-lines.txt <<'EOF'
bash/stable,now 5.2 amd64 [installed]
grep/stable,now 3.11 amd64 [installed]
nginx/stable 1.24 amd64
vim/stable,now 9.1 amd64 [installed]
EOF
```

Predict the difference:

```bash
grep -E '[installed]' package-lines.txt
grep -E '\[installed\]' package-lines.txt
```

Run them.

Explain:

```text
[installed] means:
\[installed\] means:
```

---

# Documentation habit

Run:

```bash
man 7 regex
```

Search for bracket expressions:

```text
/bracket
```

Also run:

```bash
grep --help | grep -E 'extended|perl|regexp|regex'
```

Answer:

1. What does `grep -E` mean?
2. Does your `grep` support `-P`?
3. Should he rely on `-P` for portable shell practice?

Expected answer:

```text
No. Use POSIX/basic/extended regex habits first. PCRE can be learned later.
```

---

# End standard

He is done only if he can say:

```text
I know why broad dot matches too much.
I can use character sets to be explicit.
I can escape regex metacharacters when I want literal characters.
I know that regex syntax differs by tool.
```
