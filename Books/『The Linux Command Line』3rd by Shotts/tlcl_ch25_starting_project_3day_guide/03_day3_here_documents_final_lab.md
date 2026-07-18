# Shotts Chapter 25: Starting a Project

This guide is for Chapter 25, **Starting a Project**, from William Shotts's *The Linux Command Line*.

The chapter starts the continuing shell-script project: a small HTML system-information report.

The point is not to memorize HTML or copy shell syntax. The point is to learn how a useful script grows:

```text
minimal working script
→ add one piece of data
→ name repeated/changing values
→ understand variable expansion
→ use a here document for readable output
→ keep testing after every small change
```

Feynman analogy:

```text
Do not build the whole house in your head.
First build a small frame that stands.
Then add one wall.
Then label the reusable parts.
Then replace messy repeated work with a cleaner method.
```

Working directory for all three days:

```bash
mkdir -p ~/tlcl-ch25-starting-project
cd ~/tlcl-ch25-starting-project
```

Discipline rule:

```text
Before typing: predict what the script should print.
After running: inspect the output.
Before moving on: explain what changed and why it helped.
```

---
# Day 3: Here Documents and Final Lab

## Sections to read today

Read before Exercise 1:

```text
Here Documents
```

Read after Exercise 4:

```text
Summing Up
```

Day 3 is about making multiline output readable and understanding when expansion does or does not happen inside a here document.

---

# What he should gain today

He should gain this idea:

```text
A here document lets a script feed a block of text to a command through standard input.
```

In this chapter, the command is usually `cat`, so the here document becomes a clean way to print a multiline HTML page.

Mindful question before starting:

```text
What am I gaining from this section?
```

Answer:

```text
I am learning a cleaner way to output large blocks of text without writing echo again and again.
```

---

# After reading: concept questions for “Here Documents”

Answer without looking back:

1. What is a here document?
2. What does the `<<` operator do?
3. What is the delimiter/token for?
4. Why must the closing delimiter appear exactly by itself?
5. What command is commonly used with a here document to print text?
6. What expansions happen in an unquoted here document?
7. What changes when the opening delimiter is quoted?
8. What does `<<-` do differently from `<<`?
9. Why can `<<-` be tricky with editors that insert spaces instead of tabs?

Do not start Exercise 1 until these are answered.

---

# Exercise 1: Small here-document experiment

## Skill being gained

He is learning the basic structure:

```bash
command << TOKEN
text
TOKEN
```

## Predict before typing

Write:

```text
cat will receive the lines between START and START as standard input.
cat will print those lines to standard output.
```

## Run

```bash
cat << START
one
two
three
START
```

## Explain-back

Answer:

1. What command received standard input?
2. Which lines became standard input?
3. Which line ended the here document?
4. Did `START` itself get printed?

---

# Exercise 2: Expansion inside here documents

## Skill being gained

He is learning that unquoted here documents still allow shell expansion.

## Predict before typing

Before running, predict exactly what will print:

```bash
word='hello'
cat << END
$word
"$word"
'$word'
\$word
END
```

Write your prediction first.

## Run

```bash
word='hello'
cat << END
$word
"$word"
'$word'
\$word
END
```

## Explain-back

Answer:

1. Did quotes inside the here document stop expansion?
2. What did the backslash do before `$word`?
3. Why is this surprising if you are thinking only about normal shell quotes?
4. Why might expansion be useful in an HTML report?

---

# Exercise 3: Quoted delimiter experiment

## Skill being gained

He is learning how to prevent expansion inside a here document.

## Predict before typing

Predict what changes when the delimiter is quoted:

```bash
word='hello'
cat << 'END'
$word
"$word"
'$word'
\$word
END
```

## Run

```bash
word='hello'
cat << 'END'
$word
"$word"
'$word'
\$word
END
```

## Explain-back

Answer:

1. What happened to `$word`?
2. Why did quoting the delimiter matter?
3. When would you want expansion?
4. When would you want literal text?

---

# Exercise 4: Rewrite the report using a here document

## Skill being gained

He is learning to replace repeated `echo` lines with one readable output block.

## Predict before typing

Write:

```text
The script should produce the same kind of HTML report as before.
TITLE, CURRENT_TIME, and HOSTNAME_VALUE should expand inside the here document.
The script should be easier to read.
```

## Create final Chapter 25 script

```bash
cd ~/tlcl-ch25-starting-project

cat > sys-info-page <<'SCRIPT'
#!/usr/bin/env bash

# sys-info-page - generate a simple system information page

TITLE="System Information Report"
CURRENT_TIME="$(date)"
HOSTNAME_VALUE="$(hostname)"

cat << HTML
<!doctype html>
<html>
<head>
  <title>$TITLE</title>
</head>
<body>
  <h1>$TITLE</h1>
  <p>Generated: $CURRENT_TIME</p>
  <p>Hostname: $HOSTNAME_VALUE</p>
  <p>This page was generated by a Bash script.</p>
</body>
</html>
HTML
SCRIPT

chmod +x sys-info-page
```

Syntax check:

```bash
bash -n sys-info-page
```

Run:

```bash
./sys-info-page > report.html
```

Inspect:

```bash
head report.html
grep -E '<title>|<h1>|Generated:|Hostname:' report.html
tail report.html
```

## Explain-back

Answer:

1. What did `cat << HTML` do?
2. Where did the HTML text come from?
3. Why did `$TITLE` expand?
4. Why did this remove many `echo` commands?
5. Why is the script easier to read now?
6. What would happen if the closing `HTML` delimiter had spaces after it?

---

# Exercise 5: Delimiter mistake lab

## Skill being gained

He is learning to diagnose common here-document mistakes.

Copy the script:

```bash
cp sys-info-page broken-sys-info-page
```

Edit `broken-sys-info-page` and intentionally add spaces after the closing delimiter:

```text
HTML   
```

Run syntax check:

```bash
bash -n broken-sys-info-page
```

Then try running it:

```bash
bash broken-sys-info-page > broken.html
```

Clean up:

```bash
rm -f broken-sys-info-page broken.html
```

## Explain-back

Answer:

1. What error or warning did Bash show?
2. Why did the closing delimiter fail?
3. Why is exact text important in here documents?
4. How would this mistake be harder to find in a large script?

---

# Read after Exercise 5

Now read:

```text
Summing Up
```

Mindful question:

```text
What did this chapter add to my scripting skill?
```

Answer in your own words before continuing.

---

# Final Chapter 25 self-test

Answer in writing:

1. Why did Chapter 25 start a project instead of only teaching isolated syntax?
2. Why is a minimal working script valuable?
3. What does the script print to stdout?
4. How does redirection turn stdout into a file?
5. Why are variables useful in a report script?
6. What happens when a variable is empty?
7. Why do quotes matter around variable values?
8. What does command substitution do?
9. What is a here document?
10. What is the difference between `<< TOKEN` and `<< 'TOKEN'`?
11. Why must the closing delimiter be exact?
12. Why is the here-document version easier to maintain than many `echo` lines?

---

# Final Feynman explain-back

Explain the final script to a younger student:

```text
This script creates a small HTML report.
It stores the title, time, and hostname in variables.
It uses a here document to print a whole block of HTML.
Because the here-document delimiter is not quoted, variables expand inside the HTML.
The output goes to stdout, so I can redirect it into report.html.
```

Then explain each line group:

```text
shebang:
comment:
TITLE assignment:
CURRENT_TIME assignment:
HOSTNAME_VALUE assignment:
cat << HTML:
HTML body:
closing HTML delimiter:
```

If he cannot explain a line group, he should reread the relevant section and repeat the exercise.

---

# Day 3 finish standard

He is done with Chapter 25 only if he can say:

```text
I can build a minimal script first.
I can add data one step at a time.
I can use variables for values that may be reused or changed.
I can explain expansion, quoting, and empty-variable risks.
I can use a here document to print a readable multiline output block.
I can test with bash -n and inspect the generated output.
```

He is now ready for Chapter 26, where the report should be separated into functions.
