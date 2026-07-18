#Book/Effective-Shell #Author/Kerr
# Effective Shell Chapter 4: Regular Expression Essentials

This guide is a follow-up to Author/Shotts Chapter 19, **Regular Expressions**.

<<<<<<< HEAD
=======
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

>>>>>>> origin/publish
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
# Session 6: Final Lab — Regex as a Disciplined Tool

## What to read before exercises

Before this lab, skim the whole Chapter 4 again.

Pay attention to:

```text
Managing Complexity with Regular Expressions
Quantifiers
Character Sets and Metacharacters
Anchors
Capture Groups
Greedy Matching
A Word of Warning
```

---

# Lab 1: Log level matcher

Task:

```text
Match serious log lines: ERROR or WARN at the beginning of the line.
```

Data:

```bash
cp ~/effective-shell-ch04-regex/log-lines.txt .
cat log-lines.txt
```

Build in stages:

```bash
grep -E 'ERROR|WARN' log-lines.txt
grep -E '^(ERROR|WARN)' log-lines.txt
```

---

# Lab 2: Config directive matcher

Task:

```text
Find active PasswordAuthentication setting, not comments.
```

Data:

```bash
cp ~/effective-shell-ch04-regex/config-sample.conf .
cat config-sample.conf
```

Try:

```bash
grep -E 'PasswordAuthentication' config-sample.conf
grep -E '^PasswordAuthentication' config-sample.conf
```

Explain:

```text
Why is the anchor important? It ensures that the directive is at the start of the line, not inside a comment or elsewhere. 
What did the broad search include that the strict search excluded? Commented lines. 
```

Now find both commented and uncommented forms:

```bash
grep -E '^#?PasswordAuthentication' config-sample.conf
```
---

# Lab 3: Package marker matcher

Create data:

```bash
cat > packages.txt <<'EOF'
bash/stable,now 5.2 amd64 [installed]
grep/stable,now 3.11 amd64 [installed]
nginx/stable 1.24 amd64
vim/stable,now 9.1 amd64 [installed]
EOF
```

Task:

```text
Find installed packages.
```

Explain:

```text
Why is grep -F simplest here? You only need to search for the fixed string. 
When should he avoid regex and use fixed strings? When searching for literal text that contains special regex characters.
```

---

# Lab 4: Email-ish matcher

Task:

```text
Find lines that look like a whole simple email address.
```

Data:

```bash
cp ~/effective-shell-ch04-regex/email-addresses.txt .
cat email-addresses.txt
```

Build:

```bash
grep -E '@' email-addresses.txt
grep -E '.+@.+' email-addresses.txt
grep -E '^[^@]+@[^@]+$' email-addresses.txt
grep -E '^[^@[:space:]]+@[^@[:space:]]+$' email-addresses.txt
```

Answer:

1. What invalid examples might still pass? dave@, @example.com, multiple dots, invalid domains, etc.
2. Would this be enough for a quick shell filter? Yes. 
3. Would this be enough for a production email validator? No. 
4. What would you do for production validation instead of relying only on regex? Use a proper library or validation function in your programming language. Regex alone is never sufficient for full RFC compliance. 

---

# Lab 5: Greedy HTML matcher

Data:

```bash
cp ~/effective-shell-ch04-regex/html-snippets.txt .
cat html-snippets.txt
```

Compare:

```bash
grep -E '<.+>' html-snippets.txt
grep -E '<[^>]+>' html-snippets.txt
```

Explain:

```text
Why can dot-plus be too greedy? .+ matches as much as possible, so <.+> eats everything from the first < to the last > on the line. 
Why is not-closing-bracket clearer here? <[^>]+> stops at the first >. Correctly matching one tag at a time. 
```