# Shotts Chapter 30: Troubleshooting — Three-Day Guide

Chapter 30 should be studied slowly because it changes how a student thinks.

A beginner reaction is:

```text
It failed. I will change things until it works.
```

A disciplined reaction is:

```text
It failed. I will identify the type of failure, isolate the smallest area, test one hypothesis, and explain the result.
```

## Three-day split

| Day | File | Read before exercises | Main gain |
|---|---|---|---|
| Day 1 | `01_day1_syntactic_errors_and_expansion_traps.md` | Beginning of Ch. 30 through “Logical Errors” | Recognize syntax errors, missing quotes, unexpected tokens, unanticipated expansions, and the difference between syntax and logic |
| Day 2 | `02_day2_defensive_programming_and_input_testing.md` | “Defensive Programming” through “Testing” / “Test Cases” | Prevent common failures with quoting, input validation, `set` options, ShellCheck, filename discipline, and test cases |
| Day 3 | `03_day3_debugging_tracing_and_final_lab.md` | “Debugging” through “Summing Up” | Isolate problem areas, trace execution, inspect values, and produce a disciplined debugging report |

## Main project

The project is a small script called:

```text
lab-check.sh
```

It will intentionally contain bugs. He will fix them using evidence, not guessing.

## Core debugging log

For every bug, he must fill this out:

```text
Expected behavior:
Actual behavior:
Error message or wrong output:
Bug type:
Smallest suspicious area:
Hypothesis:
Test:
Evidence:
Fix:
Retest result:
Feynman explanation:
```

## Do not skip this chapter

Chapter 30 is one of the most important chapters for becoming effective. It teaches the difference between:

```text
someone who types commands
```

and

```text
someone who can diagnose systems logically
```

## Completion standard

He is done only if he can:

```text
recognize syntax errors
recognize logical errors
quote variables safely
avoid filename traps
validate input before action
use bash -n
use bash -x
inspect variable values
create test cases
fix bugs one at a time
explain the bug without vague words like “it just broke”
```
