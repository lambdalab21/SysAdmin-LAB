# Review Quiz and Retention Drills — Effective Shell Ch. 7

Use this file after finishing Day 1–3.

Do not use notes for the first attempt.

---

# Part 1: Concept quiz

Answer in writing.

1. What does `sed` do by default with input and output?
2. Does `sed 's/a/b/' file` change `file` directly?
3. What does the `g` flag do in a substitution?
4. What does `sed -n '/ERROR/p' file` do?
5. What does `d` do in `sed '/pattern/d' file`?
6. What does `sed -e 'expr1' -e 'expr2' file` mean?
7. Why can expression order matter?
8. What does `s/$/;/` do?
9. What does `s/^/echo /` do?
10. What does `\1` mean in a replacement?
11. Why is `sed -E` often easier when using groups and `+`?
12. Why should you preview before editing a file?
13. What is one problem with `sed -i` portability?
14. When should you use `awk` instead of `sed`?
15. When should you use a small program instead of a long sed expression?

---

# Part 2: Predict the output

Create test data:

```bash
mkdir -p ~/effective-shell-ch07-review
cd ~/effective-shell-ch07-review

cat > sample.txt <<'EOF'
# comment
app01 ready
app02 ready
error on app01

port=8080
path=/var/www/site
EOF
```

For each command, predict before running.

## 1

```bash
sed 's/app/server/' sample.txt
```

Prediction:

```text

```

## 2

```bash
sed -n '/^app/p' sample.txt
```

Prediction:

```text

```

## 3

```bash
sed '/^#/d' sample.txt
```

Prediction:

```text

```

## 4

```bash
sed -e '/^#/d' -e '/^$/d' sample.txt
```

Prediction:

```text

```

## 5

```bash
sed -E -n 's/^([^=]+)=(.*)$/key=\1 value=\2/p' sample.txt
```

Prediction:

```text

```

Run only after filling the predictions.

---

# Part 3: Small transformation drills

## Drill 1: Select first, then transform

Task:

```text
Print only lines that begin with app.
```

Your command:

```bash

```

Explain:

```text

```

Now transform only those lines by replacing `ready` with `online`.

Your command:

```bash

```

Explain:

```text

```

---

## Drill 2: Remove noise

Task:

```text
Remove comments and blank lines from sample.txt.
```

Your command:

```bash

```

Explain both expressions:

```text
Expression 1:
Expression 2:
```

---

## Drill 3: Convert key-value lines

Task:

```text
Convert lines like port=8080 into port: 8080.
Only transform lines containing =.
```

Your command:

```bash

```

Explain capture groups:

```text
Group 1:
Group 2:
Replacement:
```

---

## Drill 4: Safe edit pattern

Task:

```text
Create sample.updated.txt where app01 becomes app03.
Do not modify sample.txt directly.
```

Commands:

```bash

```

Explain:

```text
Why is this safer?
How did you verify the result?
```

---

# Part 4: Tool choice quiz

Choose the tool and explain why.

| Problem | Tool | Reason |
|---|---|---|
| Find all lines with `ERROR` |  |  |
| Delete all blank lines from a config preview |  |  |
| Replace `localhost` with `app01.local` |  |  |
| Print the second field from colon-separated text |  |  |
| Count how many times each username appears |  |  |
| Parse nested JSON |  |  |
| Fill `%PORT%` and `%HOST%` in a template |  |  |

---

# Part 5: Feynman final explanation

Explain `sed` to a younger student:

```text
sed is useful because...
It reads...
It writes...
A substitution means...
An address means...
A capture group means...
The main danger is...
The disciplined habit is...
```

Minimum acceptable answer:

```text
sed is a stream editor. It reads text, applies editing rules line by line, and writes the result to stdout. A substitution replaces text matching a pattern. An address limits a command to certain lines. A capture group saves part of a match so it can be reused. The main danger is changing more text than intended or editing a file before previewing. A disciplined user builds the command in stages and inspects output before saving changes.
```

---

# Retention schedule

Review after:

```text
1 day
3 days
7 days
21 days
```

Each review should include only 10–15 minutes:

```text
predict 3 sed commands
write 2 small transformations
explain 1 capture-group command
choose grep/sed/awk/program for 3 tasks
```
