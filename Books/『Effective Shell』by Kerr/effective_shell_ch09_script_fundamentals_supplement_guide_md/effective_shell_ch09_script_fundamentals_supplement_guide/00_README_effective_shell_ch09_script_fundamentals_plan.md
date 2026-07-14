# Effective Shell Ch. 9 Supplement: Shell Script Essentials / Shell Script Fundamentals

This guide is a short supplement to Shotts, *The Linux Command Line* Part IV.

Use it after the student has completed or started:

```text
TLCL Ch. 24 — Writing Your First Script
TLCL Ch. 25 — Starting a Project
TLCL Ch. 26 — Top-Down Design
```

Effective Shell Ch. 9 mostly refreshes the fundamentals, but it adds useful emphasis on:

```text
comments that explain why, not merely what
building a useful command from previous pipeline skills
multi-line pipelines with backslash continuation
running scripts in different ways
portable shebangs with /usr/bin/env
sourcing vs executing
installing a script as a local command
```

This should not take long. Use it as a **2-day supplement plus review**.

## Files

| File | Purpose |
|---|---|
| `01_day1_script_basics_comments_and_common_command.md` | Refresh script basics, comments, and the `common` command pipeline |
| `02_day2_running_shebang_source_and_install.md` | Refresh running scripts, shebangs, sourcing, and installing a local command |
| `03_review_quiz_and_retention_drills.md` | Short review, concept checks, and retention drills |

## Core habit

```text
Do not merely type a script.
First explain the job.
Then explain the pipeline.
Then write the smallest version.
Then run it.
Then inspect and improve it.
```

## Feynman standard

After this supplement, the student should be able to explain to a younger student:

```text
A shell script is a text file of shell commands.
Comments explain the reason for decisions.
The shebang tells the operating system which interpreter should run the file.
Running a script creates a child shell.
Sourcing a script runs it in the current shell.
Installing a script means placing it somewhere the shell can find through PATH.
```

## Not the goal

The goal is **not** to memorize every execution method.

The goal is to know which method is appropriate:

```text
bash script.sh        test with Bash explicitly
./script.sh           run executable script by path
source script.sh      change current shell intentionally
command-name          run installed script found through PATH
```
