# TLCL Chapter 33: Flow Control — Looping with `for`

This is a three-day reading and practice guide for William Shotts, *The Linux Command Line*, Chapter 33.

Chapter 33 is short, but it matters because `for` loops are one of the most common ways shell scripts process lists: words, arguments, filenames, hosts, and generated sequences.

## Three-day split

| Day | Main topic | Read before exercises | Main skill gained |
|---|---|---|---|
| Day 1 | Traditional `for` form over simple word lists | Beginning of Chapter 33 through **`for`: Traditional Shell Form** | Understand what a `for` loop does with a list of words |
| Day 2 | `for` with filenames, globs, arguments, and command output | Reread **`for`: Traditional Shell Form** with attention to word expansion | Use `for` safely in realistic shell work without breaking on spaces or unmatched globs |
| Day 3 | C-language style `for` and final lab | **`for`: C Language Form** and **Summing Up** | Know when numeric loops are useful and integrate `for` into a useful script |

## Core Feynman idea

Explain a `for` loop like this:

```text
I have a list of things.
For each thing in the list, Bash gives the thing a temporary name.
Then Bash runs the same commands once for that thing.
When the list is finished, the loop stops.
```

A `for` loop is not magic. It is repeated work over a list.

## Discipline rule

Before every `for` loop, write this:

```text
List source:
What is one item?
Variable name:
Command repeated for each item:
Expected first pass:
Expected last pass:
Danger if the list is wrong:
```

## Dangerous command rule

Never start with this:

```bash
rm *.tmp
mv *.log archive/
chmod 600 *.key
```

Start with preview:

```bash
printf 'Would process: <%s>\n' *.tmp
```

Then explain what the shell expanded before using `rm`, `mv`, `cp`, `chmod`, or `chown`.

## Final standard

He is ready to move on when he can say:

```text
I know the list a for loop is using.
I know what one item means.
I can explain when the shell expands a glob before the loop begins.
I quote variables inside the loop.
I can use "$@" safely for script arguments.
I know when a C-style for loop is appropriate.
I test a loop with echo or printf before doing real work.
```
