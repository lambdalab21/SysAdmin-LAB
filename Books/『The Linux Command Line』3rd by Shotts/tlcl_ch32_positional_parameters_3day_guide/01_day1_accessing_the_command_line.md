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
# Day 1: Accessing the Command Line

## Read before exercises

Read Chapter 32 from the beginning through:

```text
Accessing the Command Line
```

## What he should gain from this reading

He should gain this idea:

```text
A shell script is not just a file of commands.
It can behave like a real command by accepting arguments from the command line.
```

He is learning the script's **interface**.

A script interface answers:

```text
What does the user type?
What does each argument mean?
What should the script do if the arguments are wrong?
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
When I run:

./backup.sh project1 backup1

the script receives the words after the script name.
It can inspect those words using special variables.
```

Analogy:

```text
The script is a worker at a counter.
The command-line arguments are the forms handed to the worker.
If the forms are missing or unclear, the worker should not guess.
```

---

# After reading: concept questions

Answer without looking back:

1. What does `$0` usually contain?
2. What does `$1` contain?
3. What does `$2` contain?
4. What does `$#` tell you?
5. Why are these called positional parameters?
6. Why should a script check the number of arguments before using them?
7. What might go wrong if `$1` is empty but the script assumes it is a filename?
8. Why should arguments usually be quoted when used?

Do not continue until these are answered.

---

# Exercise 1: See what the script receives

## Skill being gained

He is learning to inspect the command-line contract.

## Do not type yet

Before creating the script, predict the output for this command:

```bash
./show-args.sh alpha beta gamma
```

Fill in:

```text
$0 will be:
$1 will be:
$2 will be:
$3 will be:
$# will be:
```

## Create the script

```bash
cd ~/tlcl-ch32-positional-parameters

cat > show-args.sh <<'EOF'
#!/usr/bin/env bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Third argument: $3"
echo "Number of arguments: $#"
EOF

chmod +x show-args.sh
./show-args.sh alpha beta gamma
```

## Test different cases

Run these one at a time:

```bash
./show-args.sh
./show-args.sh alpha
./show-args.sh alpha beta
./show-args.sh "alpha beta" gamma
```

## Explain-back

Answer:

1. What changed when no arguments were given?
2. What changed when one argument contained a space?
3. Why did quotes around `"alpha beta"` matter?
4. Did `$#` count words or arguments?

Important explanation:

```text
The shell parses the command line first.
Then the script receives arguments.
Quoted words with spaces can remain one argument.
```

---

# Exercise 2: Add argument checking

## Skill being gained

He is learning not to trust missing input.

## Read before exercise

Reread the part of “Accessing the Command Line” that shows `$#`.

Ask:

```text
How can the script know whether the user gave enough arguments?
```

## Do not type yet

Design the rule in English:

```text
This script requires exactly two arguments:
1. source
2. destination

If the user does not give exactly two arguments, print usage and fail.
```

## Create the script

```bash
cat > require-two.sh <<'EOF'
#!/usr/bin/env bash

if [[ $# -ne 2 ]]; then
    echo "Usage: $0 SOURCE DESTINATION" >&2
    exit 1
fi

source=$1
destination=$2

echo "Source: $source"
echo "Destination: $destination"
EOF

chmod +x require-two.sh
```

## Test both branches

```bash
./require-two.sh
./require-two.sh one
./require-two.sh one two
./require-two.sh one two three
```

## Explain-back

Answer:

1. What does `[[ $# -ne 2 ]]` mean in English?
2. Which test cases should fail?
3. Which test case should pass?
4. Why does the usage message go to `stderr` with `>&2`?
5. Why does the script use `exit 1`?

---

# Exercise 3: Upgrade the report script to accept a title

## Skill being gained

He is learning to make the report script less fixed.

## Do not type yet

Define the contract:

```text
Command:
./lab-report.sh TITLE

Meaning:
Generate an HTML report using TITLE as the page title.

Required arguments:
exactly 1
```

## Create the script

```bash
cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

if [[ $# -ne 1 ]]; then
    echo "Usage: $0 REPORT_TITLE" >&2
    exit 1
fi

report_title=$1

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

report_footer () {
    echo "</body>"
    echo "</html>"
}

report_header
report_system_info
report_footer
EOF

chmod +x lab-report.sh
```

Run:

```bash
./lab-report.sh "App01 Lab Report" > report.html
grep -E '<title>|<h1>' report.html
```

## Explain-back

Answer:

1. Why must the title be quoted on the command line?
2. What would happen with `./lab-report.sh App01 Lab Report`?
3. Why is `report_title=$1` near the top?
4. Why is `$report_title` quoted in ordinary shell code but inside HTML text it appears as part of a string?

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
$0 is the script name.
$1, $2, and so on are command-line arguments by position.
$# is the number of arguments.
A disciplined script checks arguments before using them.
A script should print clear usage instead of guessing.
```

He must also be able to explain why this is dangerous:

```bash
rm $1
```

And why this is safer but still requires validation:

```bash
rm -- "$1"
```
