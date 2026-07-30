# TLCL Chapter 28: Reading Keyboard Input — Two-Day Guide

This guide is for Chapter 28, **Reading Keyboard Input**, from William Shotts's *The Linux Command Line*.

The chapter fits well into **two days**:

| Day | Main sections | Main gain |
|---|---|---|
| Day 1 | `read—Read Values from Standard Input`, options, `IFS`, here strings, “You Can’t Pipe read” | Understand how scripts receive input and how `read` assigns variables |
| Day 2 | `Validating Input`, `Menus`, `Summing Up`, optional `Extra Credit` | Learn to distrust input, validate it, and build a small interactive script |

## Why this chapter matters

Before this chapter, scripts mostly print information or run commands.

After this chapter, scripts can ask the user questions.

That is powerful, but dangerous:

```text
User input is not automatically good input.
A disciplined script must check what it receives.
```

## Feynman analogy

A script with `read` is like a clerk asking a customer for information.

A careless clerk writes down anything and causes problems later.

A disciplined clerk asks clearly, checks the answer, and refuses nonsense.

```text
Prompt clearly.
Read carefully.
Validate before using.
```

## Continuing project

Use the same project directory pattern as earlier chapters:

```bash
mkdir -p ~/tlcl-ch28-input
cd ~/tlcl-ch28-input
```

The continuing script will be:

```text
interactive-report.sh
```

It builds on the earlier report-generator idea from Chapters 25–27.

## Core discipline for Chapter 28

Before every exercise, he must answer:

```text
What question is the script asking?
What variable will receive the answer?
What valid answers should be accepted?
What invalid answers should be rejected?
What will happen if the user presses Enter?
What will happen if the user types spaces?
```

## Completion standard

He is done with Chapter 28 only if he can explain:

```text
read reads one line of standard input.
read assigns words to variables.
REPLY is used when no variable name is supplied.
-r usually makes input safer by preventing backslash interpretation.
-p gives a prompt.
IFS affects how input is split.
A here string can feed one string to read.
A pipeline can make read run in a subshell.
User input must be validated before being trusted.
A menu is just controlled input plus branching.
```
