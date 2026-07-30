# TLCL Chapter 31: Flow Control — Branching with `case`

## Two-day guide

This guide is for Chapter 31 of William Shotts's *The Linux Command Line*.

Chapter 31 is short, but it is important. It teaches a cleaner way to handle many-choice decisions in shell scripts.

The big idea:

```text
Use if when you are testing conditions.
Use case when you are choosing among known patterns or menu choices.
```

Feynman analogy:

```text
`if` is like asking a sequence of yes/no questions.
`case` is like using a labeled switchboard:
"If the input looks like this, go here.
 If it looks like that, go there.
 If nothing matches, use the default branch."
```

## How this chapter should be split

| Day | Main reading | Main gain |
|---|---|---|
| Day 1 | Read the start of Ch. 31 through `case` syntax and `Patterns` | Understand `case`, pattern matching, first-match behavior, and the default branch |
| Day 2 | Read `Performing Multiple Actions` and `Summing Up` | Use `case` in menus and real scripts; combine `case` with functions from Ch. 26 and input from Ch. 28 |

If your edition has fewer section headings, use this split:

```text
Day 1: first half of "The case Command" — syntax, choices, patterns
Day 2: second half of "The case Command" plus "Summing Up" — practical menu/script use
```

## Prerequisites to refresh before starting

He should briefly review:

```text
Ch. 26: functions
Ch. 27: if and exit status
Ch. 28: read and menus
Ch. 29: loops
Ch. 30: troubleshooting
Ch. 7: shell patterns / globs
```

Do not skip the Ch. 7 reminder. `case` uses shell pattern thinking, not normal `grep` regex thinking.

## Core disciplined-thinking habit

For every `case` statement, write this before typing:

```text
Input variable:
Possible values:
Pattern for each value:
Action for each pattern:
Default action:
Which branch should run for my test input?
```

## Final standard

He is done with Chapter 31 only if he can say:

```text
I can use case when a script must choose among known choices.
I can explain the syntax: case, in, pattern), commands, ;;, *), esac.
I can distinguish case patterns from regular expressions.
I can test valid input, invalid input, and surprising input.
I can use case with functions to make a script readable.
```
