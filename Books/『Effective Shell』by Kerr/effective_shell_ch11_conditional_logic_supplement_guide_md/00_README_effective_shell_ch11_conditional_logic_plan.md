# Effective Shell Ch. 11 Supplement: Conditional Logic

This guide supplements **TLCL Ch. 27 (`if`)**, **TLCL Ch. 31 (`case`)**, and parts of **TLCL Ch. 28–32**.

It should not take long if TLCL Ch. 27 and Ch. 31 are already understood.

Use this as a **two-day refresh plus review**.

## Why this chapter matters

Kerr Ch. 11 is useful because it reviews conditional logic in a practical style:

- `if`
- exit status
- `test` and `[ ... ]`
- `else` and `elif`
- common string, integer, and file tests
- combining tests
- `[[ ... ]]`
- regex inside `[[ ... ]]`
- command chaining with `&&` and `||`
- `case`
- improving a small real script

Most concepts overlap with TLCL, but Kerr adds useful reinforcement around **ordering conditions**, **choosing the right conditional form**, and **using conditionals to make a small shell tool more robust**.

## Suggested split

| Day | File | Main purpose |
|---|---|---|
| Day 1 | `01_day1_if_test_else_elif_refresh.md` | Refresh `if`, exit status, `test`, `[ ]`, `else`, `elif`, and ordering |
| Day 2 | `02_day2_conditional_expressions_chaining_case.md` | Practice `[[ ]]`, regex tests, `&&` / `||`, and `case` |
| Review | `03_review_quiz_and_retention_drills.md` | Retention, self-test, and practical mini-drills |

## Main discipline

```text
condition in English
→ choose the test form
→ predict true case
→ predict false case
→ test both branches
→ explain why Bash chose that branch
```

## Feynman rule

Before writing code, he must explain the decision in normal language.

Bad:

```text
I need an if statement.
```

Better:

```text
I need the script to check whether a readable history file exists.
If it exists, the script should analyze it.
If it does not exist, the script should print a clear message and stop.
```

## What he should already know from TLCL

He should already have seen:

- exit status: `0` means success, non-zero means failure
- `if ... then ... fi`
- `test` and `[ ... ]`
- file tests like `-e`, `-f`, `-d`, `-r`, `-x`
- string tests like `-z`, `-n`, `=`, `!=`
- integer tests like `-eq`, `-lt`, `-gt`
- `[[ ... ]]`
- `(( ... ))`
- `case`
- arguments and variables

## What Kerr adds or reinforces

Kerr is most useful here for:

1. Seeing conditionals as checks on command success.
2. Thinking carefully about `elif` order.
3. Recognizing the limits of `[ ... ]` and the usefulness of `[[ ... ]]`.
4. Using `&&` and `||` for short command chains.
5. Updating a real small script rather than doing isolated examples.

## Working directory

Use a disposable directory:

```bash
mkdir -p ~/effective-shell-ch11-supplement/{bin,data}
cd ~/effective-shell-ch11-supplement
```

Do not use `/usr/local/bin` for these drills. Use the local `bin/` directory in this lab.
