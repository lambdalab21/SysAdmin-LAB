# TLCL Chapter 27: Branching with `if` — Four-Day Guide

A cleaner phrasing: “Create Markdown file(s) for Chapter 27 by Shotts. Use the Feynman method and split them into Day 1, Day 2, Day 3, and Day 4.”

Chapter 27 should be split into four days because it introduces several different ideas that beginners often mix together:

```text
if syntax
exit status
classic test / [ ]
modern [[ ]]
arithmetic (( ))
compound conditions
control operators && and ||
```

## Four-day split

| Day | File | Read before exercises | Main gain |
|---|---|---|---|
| Day 1 | `01_day1_if_and_exit_status.md` | `if Statements`, `Exit Status` | Understand that `if` branches on command success/failure |
| Day 2 | `02_day2_using_test_file_string_integer_checks.md` | `Using test` | Use file, string, and integer tests deliberately |
| Day 3 | `03_day3_modern_test_and_integer_evaluation.md` | `A More Modern Version of test`, `(( ))—Designed for Integers` | Prefer `[[ ]]` for tests and `(( ))` for arithmetic decisions |
| Day 4 | `04_day4_combining_expressions_control_operators_final_lab.md` | `Combining Expressions`, `Control Operators`, `Summing Up` | Combine conditions and finish a practical branching report script |

## What Chapter 27 adds to Chapters 24–26

Chapter 24 taught:

```text
A script is a saved set of shell commands.
```

Chapter 25 taught:

```text
A script can generate useful output, such as an HTML report.
```

Chapter 26 taught:

```text
A script should be organized with functions and top-down design.
```

Chapter 27 adds:

```text
A script can choose different actions based on evidence.
```

## The disciplined pattern

Every `if` exercise must start with this written form:

```text
Condition in English:
Command or test:
Expected success case:
Expected failure case:
How I will verify:
```

Example:

```text
Condition in English:
The file named by $file exists and is a regular file.

Command or test:
[[ -f $file ]]

Expected success case:
Print that the file exists.

Expected failure case:
Print that the file is missing or not a regular file.

How I will verify:
Test once with an existing file and once with a missing file.
```

## Do not move on when he can merely type syntax

He should move on only when he can explain:

```text
if chooses based on exit status.
0 means success/true.
Nonzero means failure/false.
[ ] is the test command form.
[[ ]] is Bash's safer modern conditional expression.
(( )) is for arithmetic evaluation.
&& and || can branch, but can become hard to read if overused.
```
