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
# Session 5: Tool Differences and Manpage Habits

## What to read before exercises

Read these Chapter 4 sections:

```text
Avoiding Advanced Topics — Backtracking, Lookarounds and Atomic Grouping
A Word of Warning
Summary
```

## What he should gain from this reading

He should gain this idea:

```text
Regex is not exactly identical in every tool.
A disciplined shell user checks which regex language the tool supports.
```

This matters because examples from websites may use regex features that do not work in `grep`, `sed`, or `awk` the same way.

---

## Feynman analogy

Regex is like “English,” but tools speak different dialects.

```text
grep basic regex
 grep -E extended regex
 GNU grep -P Perl-compatible regex
 sed regex
 awk regex
 JavaScript regex
 Python regex
```

They overlap, but they are not identical.

A disciplined user asks:

```text
Which tool is reading this regex?
Which regex dialect does it use?
```

---

# After reading: concept questions

Answer before typing:

1. Why should advanced regex topics be avoided at this stage?
2. What is the danger of copying regexes from the internet?
3. What is a regex dialect?
4. Why might a regex work on a website but fail in `grep`?
5. What should he check before using advanced syntax?
6. Why is “break the problem into smaller steps” often better than one huge regex?

---

# Exercise 1: Ask the tool what it supports

Run:

```bash
grep --help | head -40
grep --help | grep -E 'basic|extended|perl|regexp|regex'
```

Answer:

```text
Option for basic regex:
Option for extended regex:
Option for Perl-compatible regex, if present:
```

---

# Exercise 2: Use `man` deliberately

Run:

```bash
man grep
```

Inside `man`, practice:

```text
/REGEXP
/extended
/-E
/-P
n
N
q
```

Write:

```text
What does -E do?
What does -F do?
What does -P do, if available?
What option would I use for fixed strings, not regex?
```

Expected idea:

```text
-F treats the pattern as a fixed string. This can be safer when you do not need regex.
```

---

# Exercise 3: Decide regex or fixed string

For each task, choose `grep`, `grep -E`, or `grep -F`.

1. Find lines containing literal `[installed]`.
2. Find lines beginning with `ERROR` or `WARN`.
3. Find lines containing the literal string `a+b`.
4. Find lines containing any digit.
5. Find lines containing either `alice` or `bob`.

Suggested answers:

```bash
grep -F '[installed]' file
grep -E '^(ERROR|WARN)' file
grep -F 'a+b' file
grep -E '[[:digit:]]' file
grep -E 'alice|bob' file
```

Explain why each choice was made.

---

# Exercise 4: Break one big regex into pipeline thinking

Instead of writing one giant email validator, use stages.

```bash
cp email-addresses.txt session5/
cd session5
```

Stage 1: contains `@`.

```bash
grep -E '@' email-addresses.txt
```

Stage 2: must have something before and after `@`.

```bash
grep -E '.+@.+' email-addresses.txt
```

Stage 3: must be the whole line.

```bash
grep -E '^[^@]+@[^@]+$' email-addresses.txt
```

Stage 4: remove lines with spaces.

```bash
grep -E '^[^@[:space:]]+@[^@[:space:]]+$' email-addresses.txt
```

Explain:

```text
What did each stage improve?
What false positives remain?
Is the regex perfect?
Does it need to be perfect for the current task?
```

---

# Documentation habit checklist

Before using a regex feature, ask:

```text
Did I check grep --help?
Did I check man grep?
Did I check man 7 regex or equivalent?
Am I using grep, grep -E, grep -F, or grep -P?
Could a fixed string be safer?
```

---

# End standard

He is done only if he can say:

```text
I know regex features differ across tools.
I can choose grep -F when I do not need regex.
I can choose grep -E for extended regex.
I know not to copy advanced regex blindly.
I know how to use man/help to verify syntax.
```
