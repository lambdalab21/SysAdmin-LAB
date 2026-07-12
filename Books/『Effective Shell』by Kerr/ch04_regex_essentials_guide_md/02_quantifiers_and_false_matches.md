#Book/Effective-Shell #Author/Kerr
# Effective Shell Chapter 4: Regular Expression Essentials

This guide is a follow-up to Author/Shotts Chapter 19, **Regular Expressions**.

Main purpose:

```text
Author/Shotts Chapter 19 introduced regex symbols.
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
# Session 2: Quantifiers and False Matches

## What to read before exercises

Read this Chapter 4 section:

```text
Building Regexes — Quantifiers
```

Also review the quick reference for:

```text
*
+
?
{10}
{10,}
{10,20}
```

## What he should gain from this reading

He should gain this idea:

```text
Quantifiers answer “how many?”
```

The main discipline is to avoid vague quantifiers when a stricter one is better.

---

## Feynman analogy

Imagine a teacher checking homework pages.

```text
*  = zero or more pages
+  = one or more pages
?  = zero or one page
{3} = exactly three pages
{3,5} = three to five pages
```

The difference between zero and one matters.

That is why:

```text
.*@.*
```

can match `@yahoo.com`, but:

```text
.+@.+
```

requires at least something before and after the `@`.

---

# After reading: concept questions

Answer before typing:

1. What does `*` mean?
2. What does `+` mean?
3. Why did `.*@.*` match too much?
4. Why is `.+@.+` stricter?
5. What does `{3}` mean?
6. What does `{3,}` mean?
7. What does `{3,5}` mean?

---

# Exercise 1: Compare `*` and `+`

Copy data:

```bash
cp email-addresses.txt session2/
cd session2
```

Before running, predict which lines will be removed when moving from `*` to `+`.

Run:

```bash
grep -E '.*@.*' email-addresses.txt
printf '\n--- stricter ---\n'
grep -E '.+@.+' email-addresses.txt
```

Answer:

```text
Which lines disappeared?
Why?
```

---

# Exercise 2: Quantifier mini-lab

Create data:

```bash
cat > repeat.txt <<'EOF'
a
ab
abb
abbb
abbbb
ac
abc
EOF
```

Predict first:

```text
ab* should match:
ab+ should match:
ab{2} should match:
ab{2,3} should match:
```

Run:

```bash
grep -E 'ab*' repeat.txt
grep -E 'ab+' repeat.txt
grep -E 'ab{2}' repeat.txt
grep -E 'ab{2,3}' repeat.txt
```

Explain any surprising output.

---

# Exercise 3: Make the email regex stricter, one step only

Current pattern:

```text
.+@.+
```

It requires text before and after `@`.

Run:

```bash
grep -E '.+@.+' email-addresses.txt
```

Now explain:

1. Which invalid lines still match?
2. Why do they still match?
3. What one problem should be fixed next?

Do not fix everything at once.

---

# Documentation habit

Run:

```bash
man 7 regex
```

Search for interval expressions:

```text
/interval
```

If `man 7 regex` is unavailable, try:

```bash
man grep
info grep
```

Answer:

1. Where did you find information about `{m,n}`?
2. Was it easy to find?
3. What search term helped?

---

# End standard

He is done only if he can explain:

```text
* allows zero matches.
+ requires at least one match.
{} can specify exact or ranged counts.
A stricter regex should be built to solve one false-positive problem at a time.
```
