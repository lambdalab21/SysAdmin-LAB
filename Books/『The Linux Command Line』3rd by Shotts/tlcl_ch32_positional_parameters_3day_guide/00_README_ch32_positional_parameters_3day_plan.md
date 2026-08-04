# TLCL Chapter 32: Positional Parameters — Three-Day Guide

Chapter 32 should be split into three days.

| Day | File | Read before exercises | Main gain |
|---|---|---|---|
| Day 1 | `01_day1_accessing_the_command_line.md` | Start of Ch. 32 through “Accessing the Command Line” | Understand `$0`, `$1`, `$2`, `$#`, and the idea that scripts receive arguments |
| Day 2 | `02_day2_handling_parameters_en_masse.md` | “Handling Positional Parameters en Masse” | Understand `shift`, `$*`, `$@`, and especially why `"$@"` matters |
| Day 3 | `03_day3_more_complete_application_final_lab.md` | “A More Complete Application” and “Summing Up” | Build a more useful script that checks arguments, prints usage, loops through targets, and fails clearly |

Main skill:

```text
Turn a script from a fixed toy program into a command-line tool.
```

Minimum standard for moving on:

```text
He can explain what each argument means,
show how Bash stores it,
validate it,
loop through multiple arguments safely,
and explain why "$@" is usually safer than $*.
```

What not to do:

```text
Do not type $1, $2, shift, or "$@" because the book says so.
First say what the user typed, then say what the script receives.
```

Final command-line contract he should be able to design:

```bash
./lab-report.sh HOSTNAME SECTION...
```

Example:

```bash
./lab-report.sh app01 system disk home
```

Plain English meaning:

```text
Create a lab report for host app01 and include the system, disk, and home sections.
```
