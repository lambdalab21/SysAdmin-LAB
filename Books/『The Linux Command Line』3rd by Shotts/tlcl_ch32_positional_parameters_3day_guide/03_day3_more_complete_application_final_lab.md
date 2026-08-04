# TLCL Chapter 32: Positional Parameters

This guide is for Chapter 32, **Positional Parameters**, from William Shotts's *The Linux Command Line*.

The goal is not merely to memorize `$1`, `$2`, and `$@`.

The goal is to learn this habit:

```text
A script is a command.
A command receives arguments.
A disciplined script checks, explains, and handles those arguments carefully.
```

Feynman analogy:

```text
A positional parameter is like a numbered envelope handed to the script.
$1 is the first envelope, $2 is the second envelope, and so on.
The script must know what each envelope is supposed to contain before it opens it.
```

Working directory for all three days:

```bash
mkdir -p ~/tlcl-ch32-positional-parameters
cd ~/tlcl-ch32-positional-parameters
```

Continuing project:

```text
lab-report.sh
```

Chapter 32 upgrades the earlier report script so it can receive command-line arguments.

Core discipline:

```text
Before writing code, define the command-line contract:
What arguments does the script expect?
Which arguments are optional?
What happens if the user gives too few, too many, or strange arguments?
```

---
# Day 3: A More Complete Application and Final Lab

## Read before exercises

Read the Chapter 32 sections:

```text
A More Complete Application
Summing Up
```

## What he should gain from this reading

He should gain this idea:

```text
A useful script has a command-line interface, argument checks, useful error messages, and predictable behavior.
```

Chapter 32 is where scripts become more command-like.

He is not just writing Bash syntax. He is designing a small tool.

---

# Before reading: Feynman preview

Imagine giving a tool to another person.

Bad tool:

```text
Run this script somehow. Try some words. Maybe it works.
```

Good tool:

```text
Usage: ./lab-report.sh [OPTIONS] REPORT_TITLE SECTION...
Sections: system disk home all
```

A disciplined script teaches the user how to use it.

---

# After reading: concept questions

Answer without looking back:

1. What makes a script feel like a real command-line application?
2. Why should a script have a usage function?
3. Why should invalid input fail early?
4. Why is it better to test many argument combinations than one happy path?
5. What does `"$@"` preserve?
6. What does `shift` change?
7. Why should error messages usually go to `stderr`?
8. Why should a script exit nonzero after argument errors?

Do not continue until these are answered.

---

# Exercise 1: Write the command contract before the script

## Skill being gained

He is learning interface-first thinking.

## Do not type yet

Write the contract:

```text
Script name:
lab-report.sh

Purpose:
Generate a simple HTML lab report.

Usage:
./lab-report.sh REPORT_TITLE SECTION...
./lab-report.sh --help

Valid sections:
system disk home all

Failure cases:
missing title
missing section
unknown section
```

Now answer:

1. Which arguments are required?
2. Which argument comes first?
3. Can sections appear in any order?
4. What should `--help` do?
5. Should `--help` be success or failure?

---

# Exercise 2: Build the complete version

## Skill being gained

He is learning to combine positional parameters, `case`, functions, and validation.

## Create the script

```bash
cd ~/tlcl-ch32-positional-parameters

cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

usage () {
    cat <<USAGE
Usage: $0 REPORT_TITLE SECTION...
       $0 --help

Generate a simple HTML lab report.

Sections:
  system   include hostname, date, and kernel information
  disk     include disk-space information
  home     include home-directory size
  all      include all report sections
USAGE
}

fail () {
    echo "Error: $1" >&2
    echo >&2
    usage >&2
    exit 1
}

report_header () {
    echo "<html>"
    echo "<head><title>$report_title</title></head>"
    echo "<body>"
    echo "<h1>$report_title</h1>"
}

report_system_info () {
    echo "<h2>System Information</h2>"
    echo "<pre>"
    hostname
    date
    uname -a
    echo "</pre>"
}

report_disk_space () {
    echo "<h2>Disk Space</h2>"
    echo "<pre>"
    df -h
    echo "</pre>"
}

report_home_space () {
    echo "<h2>Home Directory</h2>"
    echo "<pre>"
    du -sh "$HOME" 2>/dev/null
    echo "</pre>"
}

report_all () {
    report_system_info
    report_disk_space
    report_home_space
}

report_footer () {
    echo "</body>"
    echo "</html>"
}

if [[ ${1:-} == "--help" ]]; then
    usage
    exit 0
fi

if [[ $# -lt 2 ]]; then
    fail "missing REPORT_TITLE or SECTION"
fi

report_title=$1
shift

report_header

for section in "$@"; do
    case "$section" in
        system)
            report_system_info
            ;;
        disk)
            report_disk_space
            ;;
        home)
            report_home_space
            ;;
        all)
            report_all
            ;;
        *)
            fail "unknown section: $section"
            ;;
    esac
done

report_footer
EOF

chmod +x lab-report.sh
```

---

# Exercise 3: Test like a disciplined thinker

## Skill being gained

He is learning to test the interface, not just the happy path.

## Do not type yet

Fill in expected result before running:

| Command | Should succeed? | Why? |
|---|---:|---|
| `./lab-report.sh --help` | | |
| `./lab-report.sh` | | |
| `./lab-report.sh "Title Only"` | | |
| `./lab-report.sh "My Report" system` | | |
| `./lab-report.sh "My Report" all` | | |
| `./lab-report.sh "My Report" bad` | | |

## Run tests

```bash
./lab-report.sh --help
printf 'exit=%s
' "$?"

./lab-report.sh
printf 'exit=%s
' "$?"

./lab-report.sh "Title Only"
printf 'exit=%s
' "$?"

./lab-report.sh "My Report" system > report.html
printf 'exit=%s
' "$?"
grep -E '<h1>|<h2>' report.html

./lab-report.sh "My Report" all > report.html
printf 'exit=%s
' "$?"
grep -E '<h1>|<h2>' report.html

./lab-report.sh "My Report" bad > report.html
printf 'exit=%s
' "$?"
```

## Explain-back

Answer:

1. Which tests succeeded?
2. Which tests failed?
3. Did the failure cases exit nonzero?
4. Did error messages go to the terminal even when stdout was redirected?
5. Why is that useful?

---

# Exercise 4: Understand `${1:-}`

## Skill being gained

He is learning to avoid errors when a variable may be unset or empty.

In the script:

```bash
if [[ ${1:-} == "--help" ]]; then
```

Explain:

```text
Use the first argument if it exists.
If it does not exist, use an empty string instead.
```

Now test this small script:

```bash
cat > default-demo.sh <<'EOF'
#!/usr/bin/env bash

echo "First argument with default: <${1:-empty}>"
EOF

chmod +x default-demo.sh
./default-demo.sh
./default-demo.sh hello
```

Answer:

1. What printed when no argument was given?
2. What printed when an argument was given?
3. Why might this be useful in scripts that check optional arguments?

---

# Exercise 5: Final Feynman explanation

Explain the final `lab-report.sh` to a younger student:

```text
This script expects a title and at least one section name.
The title is saved before shift.
Then shift removes the title from the positional parameters.
The remaining arguments are section names.
The script loops over "$@" so each section argument is preserved.
case chooses which report function to call.
If the user gives bad input, fail prints an error and exits nonzero.
```

Now explain these one by one:

```text
usage:
fail:
report_header:
report_system_info:
report_disk_space:
report_home_space:
report_all:
report_footer:
```

---

# Final concept questions

Answer in writing:

1. What are positional parameters?
2. Why is `$1` positional rather than named?
3. What does `$#` count?
4. What does `shift` do?
5. Why is `"$@"` important?
6. What is the difference between `"$@"` and `"$*"`?
7. Why should a script print usage?
8. Why should argument errors go to `stderr`?
9. Why should invalid input exit nonzero?
10. What changed between the Chapter 25 report script and the Chapter 32 report script?

---

# Day 3 finish standard

He is done with Chapter 32 only if he can say:

```text
I can design a command-line interface for a script.
I can read individual arguments with $1, $2, and so on.
I can check the number of arguments with $#.
I can use shift to remove arguments I have already handled.
I can loop safely over remaining arguments with "$@".
I can print usage, reject bad input, and test success and failure cases.
```

He should not move on until he can write a small argument-using script from scratch without copying.
