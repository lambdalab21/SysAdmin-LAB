# Effective Shell Ch. 10 — Variables Supplement Guide

This guide is for **Effective Shell**, Chapter 10: **Using Variables to Store, Read, and Manipulate Data**.

Use it as a supplement to **The Linux Command Line** by Shotts.

It should refresh:

- TLCL Ch. 7: expansion and quoting
- TLCL Ch. 24–26: scripts, variables, constants, functions, and local variables
- TLCL Ch. 28, 34, 35 later: reading input, strings/numbers, arrays

This should not become a long detour. Most of the ideas overlap with TLCL. The main value is practical fluency:

```text
write variables safely
quote expansions correctly
know when a variable is inherited by child processes
use braces when variable names touch other text
capture command output deliberately
use read and arithmetic without guessing
recognize arrays without overusing them
```

## Suggested split

| Day | File | Main gain |
|---|---|---|
| Day 1 | `01_day1_variable_basics_export_quoting.md` | Variable assignment, expansion, environment variables, quoting, braces |
| Day 2 | `02_day2_command_substitution_read_arithmetic.md` | Store command output, read input, do simple arithmetic |
| Day 3 | `03_day3_arrays_parameter_expansion_final_lab.md` | Arrays, associative arrays, parameter expansion, final script improvement |
| Review | `04_review_quiz_and_retention_drills.md` | Memory refresh and spaced retrieval |

## Discipline rule

Before every command, write or say:

```text
What variable exists before this command?
What expansion do I expect?
Will the shell split or glob the result?
Should this variable be quoted?
Is this variable needed only in this shell, or by child processes too?
```

## Feynman analogy

A shell variable is like a labeled sticky note.

```text
name="app01"
```

But the sticky note is not automatically handed to every child process. For that, it must become an environment variable with `export`.

Quoting is like putting the sticky note inside a protective envelope so its spaces and special characters are not broken apart by the shell.

## Final standard

He is done when he can explain these without notes:

```text
name=value
$name
${name}
"$name"
export name
command=$(command)
read -r var
$(( expression ))
array=(one two three)
"${array[@]}"
```

He should also be able to say when he would **not** use Bash for data structures and would switch to Python.
