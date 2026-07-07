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
# Session 4: Anchors, Capture Groups, and Greedy Matching

## What to read before exercises

Read these Chapter 4 sections:

```text
Building Regexes — Anchors
Building Regexes — Capture Groups
Building Regexes — Lazy and Greedy Expressions
```

## What he should gain from this reading

He should gain these ideas:

```text
Anchors make matches more specific.
Groups let you reason about parts.
Greedy patterns can match more than expected.
```

---

## Feynman analogy

A search without anchors is like saying:

```text
Find a word anywhere on the page.
```

A search with anchors is like saying:

```text
The whole line must be exactly this kind of thing.
```

Groups are like drawing boxes around parts of the match.

Greedy matching is like a kid taking the biggest possible slice of cake.

---

# After reading: concept questions

Answer before typing:

1. What does `^` mean?
2. What does `$` mean?
3. What is the difference between matching a substring and matching a whole line?
4. What is a capture group?
5. What does greedy mean?
6. Why can `<.+>` match too much in HTML-like text?

---

# Exercise 1: Add anchors to email matching

```bash
cp email-addresses.txt session4/
cd session4
```

Compare:

```bash
grep -E '[^@]+@[^@]+' email-addresses.txt
printf '\n--- anchored ---\n'
grep -E '^[^@]+@[^@]+$' email-addresses.txt
```

Answer:

1. Which lines disappeared after adding anchors?
2. Why did `to: dave@effective-shell.com` disappear or remain?
3. Why did `dave@effective-shell.com <Dave Kerr>` disappear or remain?
4. What does it mean to match the whole line?

---

# Exercise 2: Anchors in logs and configs

```bash
cp ../log-lines.txt . 2>/dev/null || cp ~/effective-shell-ch04-regex/log-lines.txt .
cp ../config-sample.conf . 2>/dev/null || cp ~/effective-shell-ch04-regex/config-sample.conf .
```

Run:

```bash
grep -E 'ERROR' log-lines.txt
grep -E '^ERROR' log-lines.txt
grep -E 'error' log-lines.txt
grep -Ei '^error' log-lines.txt
```

Explain:

```text
ERROR matches:
^ERROR matches:
-i changes:
```

Config practice:

```bash
grep -E 'PasswordAuthentication' config-sample.conf
grep -E '^PasswordAuthentication' config-sample.conf
grep -E '^#?PasswordAuthentication' config-sample.conf
```

Explain:

```text
Why is ^ important in config files?
What does #? mean?
```

---

# Exercise 3: Groups as thinking boxes

Run:

```bash
grep -E '(.+)@(.+)' email-addresses.txt
```

Do not worry yet about extracting the groups. That comes later with tools like `sed`.

Explain:

```text
The first group is:
The @ is:
The second group is:
```

Question:

```text
Why can this still match too much?
```

---

# Exercise 4: Greedy matching

```bash
cp ~/effective-shell-ch04-regex/html-snippets.txt .
cat html-snippets.txt
```

Run:

```bash
grep -E '<.+>' html-snippets.txt
```

Now use a more explicit pattern:

```bash
grep -E '<[^>]+>' html-snippets.txt
```

Answer:

1. Why can `<.+>` match too much?
2. What does `[^>]` mean?
3. Why is `<[^>]+>` often easier to reason about?

Important shell-tool note:

```text
Lazy syntax such as .+? is common in some regex engines, but not all shell tools support it the same way. For shell practice, prefer explicit character sets when possible.
```

---

# Documentation habit

Run:

```bash
man grep
man 7 regex
```

Search terms:

```text
/^REGULAR EXPRESSIONS
/anchor
/repetition
```

Answer:

1. Did the man page mention anchors?
2. Did the man page mention grouping?
3. Were lazy quantifiers documented for your shell tools?
4. Why does that matter?

---

# End standard

He is done only if he can explain:

```text
Anchors prevent accidental substring matches.
Groups help reason about parts of a match.
Greedy patterns can consume more text than expected.
Explicit negated character sets are often safer than broad dot patterns.
```
