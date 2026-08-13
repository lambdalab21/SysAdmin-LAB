# Effective Shell Ch. 11 Review Quiz and Retention Drills

This review should be done one day after Day 2, then again one week later.

## Part 1: Explain from memory

Answer without notes.

1. What does exit status `0` mean?
2. What does a non-zero exit status usually mean?
3. What does `if command; then ... fi` really test?
4. What is `test`?
5. Why is `[ ... ]` not just punctuation?
6. Why do spaces matter in `[ -f file ]`?
7. When should you use `[[ ... ]]` instead of `[ ... ]`?
8. What does `=~` do?
9. What does `&&` do?
10. What does `||` do?
11. When is `case` clearer than `elif`?
12. What is the default branch in `case`?

---

# Part 2: Predict the branch

For each example, predict the output before running.

```bash
name=""
if [[ -z "$name" ]]; then echo empty; else echo filled; fi
```

```bash
name="server01"
if [[ "$name" =~ ^server[0-9]+$ ]]; then echo valid; else echo invalid; fi
```

```bash
[ -d /tmp ] && echo tmp-exists
```

```bash
[ -f /tmp ] || echo tmp-is-not-a-regular-file
```

```bash
answer="yes"
case "$answer" in
    y|yes) echo yes ;;
    n|no) echo no ;;
    *) echo unknown ;;
esac
```

Explain why each branch ran.

---

# Part 3: Choose the conditional form

For each situation, choose `[ ]`, `[[ ]]`, `case`, `&&`, or `||`.

1. Check whether a file exists.
2. Check whether a variable is empty.
3. Check whether a string matches a regex.
4. Choose between `start`, `stop`, `restart`, and `status`.
5. Run `deploy` only if `build` succeeds.
6. Print a warning if a file is missing.
7. Check whether a number is between 1 and 10.
8. Accept `y`, `Y`, `yes`, or `YES` as confirmation.

Then explain why.

---

# Part 4: Fix the bug

## Bug 1

```bash
file="my notes.txt"
if [ -f $file ]; then
    echo "exists"
fi
```

Questions:

1. What is wrong?
2. What happens if the filename contains spaces?
3. Fix it.

## Bug 2

```bash
if [ -e common ]; then
    echo "common is executable"
elif [ -x common ]; then
    echo "common exists but is not executable"
fi
```

Questions:

1. What is wrong with the order?
2. Which condition is broader?
3. Fix it.

## Bug 3

```bash
if [[ "$year" -ge 1980 && "$year" -lt 1990 ]]; then
    echo "1980s"
fi
```

Question:

```text
What should be checked before numeric comparison if year comes from user input?
```

---

# Part 5: Mini-drills

## Drill 1: File readiness

Write a script that checks whether `data/input.txt`:

1. exists,
2. is a regular file,
3. is readable,
4. is not empty.

Use clear messages for each failure.

Before coding, fill out:

```text
Condition order:
Why this order:
Expected success case:
Expected failure cases:
```

## Drill 2: Argument validation

Write a script that accepts one argument: `dev`, `test`, or `prod`.

Use `case`.

For invalid input, print usage and exit non-zero.

## Drill 3: Regex validation

Write a script that accepts a hostname like:

```text
app01
app02
db01
web12
```

Use this rule:

```text
letters followed by two digits
```

Write the regex yourself before looking at an answer.

Possible answer:

```bash
^[a-z]+[0-9]{2}$
```

Then explain every symbol.

## Drill 4: Safe command chaining

Use command chaining to express:

```text
Create a directory, and only if that succeeds, create a file inside it.
```

Then rewrite the same logic with `if`.

Answer:

```text
Which version is clearer inside a script?
Which version is convenient interactively?
```

---

# Final Feynman explanation

Explain Ch. 11 to a younger student:

```text
A shell script makes decisions by checking whether commands succeed.
The test command is a special command for asking questions about files, strings, and numbers.
Single brackets are common test syntax.
Double brackets are Bash's stronger conditional syntax.
Command chaining is a short way to say “do this only if that succeeded” or “do this if that failed.”
Case is useful when one value can match several possible patterns.
A disciplined script tests every branch, including bad input.
```

If he cannot explain that clearly, review Day 1 and Day 2 before moving on to loops or error handling.
