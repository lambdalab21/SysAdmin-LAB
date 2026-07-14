#Book/The-Linux-Command-Line #Author/Shotts 
#shell-script
# Author/Shotts Chapter 25: Starting a Project

This guide is for Chapter 25, **Starting a Project**, from William Author/Shotts's *The Linux Command Line*.

The goal is not to type an HTML-report script mindlessly. The goal is to learn how a script grows from a tiny working version into a useful project.

Core habit:

```text
small working version
→ inspect output
→ explain every line
→ improve one small part
→ test again
```

Feynman analogy:

```text
A script project is like building a small model house.
You do not begin with plumbing, paint, furniture, and wiring all at once.
You first build a simple frame that stands.
Then you add one feature at a time.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch25-project
cd ~/tlcl-ch25-project
```

Daily discipline:

```text
Before typing:
  What am I trying to make the script do?
  What output do I expect?
  Which line is responsible for which part?

After running:
  Did it run?
  Did it produce the expected output?
  Can I explain every line?
  What is the next smallest improvement?
```

---
# Day 3: Variables, Constants, and Assignment

## Sections to read before exercises

Read Chapter 25 sections:

```text
Variables and Constants
Assigning Values to Variables and Constants
```

## What he should be gaining

He should learn this habit:

```text
Use names for values that have meaning.
```

Variables are not decoration. They reduce repetition and make purpose clearer.

---

# Before reading: Feynman preview

Imagine writing a school report where the title appears in several places.

Bad:

```text
Type the title again and again.
If the title changes, fix every copy manually.
```

Better:

```text
Store the title once under a meaningful name.
Use the name where needed.
```

That is why scripts use variables.

---

# After reading: concept questions

Answer without looking back:

1. What is a variable?
2. What is a constant in a shell-script style sense?
3. Why are uppercase names often used for constants?
4. Why must there be no spaces around `=` in an assignment?
5. What is the difference between assigning a value and expanding a variable?
6. Why should variable expansions usually be quoted?
7. What does `$TITLE` mean?
8. What does `"$TITLE"` protect against?

---

# Exercise 1: Add meaningful names

## Skill being trained

He is learning to replace repeated literal values with named values.

## Predict before typing

Answer:

```text
What repeated text appears in my script?
What name could represent it?
Where will the variable be expanded?
```

## Rewrite the top of the script

```bash
cat > sys_info_page.sh <<'EOF'
#!/usr/bin/env bash

# sys_info_page.sh - generate a simple system information page

TITLE="System Information Report"

 echo "<html>"
echo "<head>"
echo "<title>$TITLE</title>"
echo "</head>"
echo "<body>"
echo "<h1>$TITLE</h1>"

echo "<h2>System Identity</h2>"
echo "<pre>"
hostname
date
uname -a
echo "</pre>"

echo "<h2>Disk Space</h2>"
echo "<pre>"
df -h
echo "</pre>"

echo "</body>"
echo "</html>"
EOF
```

Run syntax check:

```bash
bash -n sys_info_page.sh
```

If syntax is okay, run:

```bash
bash sys_info_page.sh > sys_info_page.html
grep "System Information Report" sys_info_page.html
```

## Notice and fix formatting

There is an extra space before one `echo` line:

```bash
 echo "<html>"
```

It still runs, but fix it for cleanliness.

## Explain-back

Answer:

1. What value does `TITLE` hold?
2. Where is `$TITLE` expanded?
3. Why is this better than typing the title twice?
4. Why did we use `bash -n` before running?

---

# Exercise 2: Assignment mistakes

## Skill being trained

He is learning that shell assignment syntax is strict.

## Predict before typing

Answer:

```text
Will TITLE = "Report" work?
Why or why not?
```

Try in a disposable script:

```bash
cat > assignment-test.sh <<'EOF'
#!/usr/bin/env bash

TITLE = "Bad Assignment"
echo "$TITLE"
EOF

bash assignment-test.sh
```

Now fix it:

```bash
cat > assignment-test.sh <<'EOF'
#!/usr/bin/env bash

TITLE="Good Assignment"
echo "$TITLE"
EOF

bash assignment-test.sh
rm -f assignment-test.sh
```

## Explain-back

Answer:

1. Why did the bad version fail?
2. What did Bash think `TITLE` was?
3. What is the correct assignment form?
4. Why is this easy to get wrong if coming from other languages?

---

# Exercise 3: Quoting variable expansions

## Skill being trained

He is learning that variable expansion can split into words if unquoted.

## Predict before typing

Answer:

```text
What happens if a variable contains spaces?
Why might quotes matter?
```

Try:

```bash
NAME="System Information Report"
printf '<%s>
' $NAME
printf '<%s>
' "$NAME"
```

## Explain-back

Answer:

1. How many arguments did the unquoted version produce?
2. How many arguments did the quoted version produce?
3. Why should `"$TITLE"` be the default habit?

---

# Day 3 finish standard

He is done only if he can say:

```text
I can assign a variable with no spaces around =.
I can expand a variable with $NAME.
I understand why quoted expansion is safer.
I can use variables to make a script clearer and easier to change.
I can use bash -n to catch syntax mistakes before running.
```
