# Day 2: Validating Input, Menus, and an Interactive Report

## Read before exercises

Read these Chapter 28 sections:

```text
Validating Input
Menus
Summing Up
```

Read `Extra Credit` only if time remains.

## What he should gain from this reading

He should gain this mental model:

```text
Input is untrusted until the script checks it.
A menu is just input plus validation plus branching.
```

A script that asks for input but does not validate it is incomplete.

---

# Before-reading Feynman preview

Explain this before reading:

```text
If a script asks “Enter 1, 2, or 3,” a human might type:
1
2
banana
empty input
1 2
../../weird
```

A disciplined script must decide what to do with each case.

---

# After-reading concept questions

Answer without looking back:

1. Why must scripts validate input?
2. Why is validation especially important before destructive actions?
3. How can `[[ -z "$var" ]]` help validate input?
4. How can regex help validate input?
5. Why should error messages often go to stderr?
6. What is a menu-driven program?
7. What are valid menu choices in a simple numbered menu?
8. What should the script do when the user enters an invalid choice?
9. Why should validation happen before action?
10. How does Chapter 28 reuse ideas from Chapter 27?

Do not continue until these are answered.

---

# Exercise 1: Reject empty input

## Section to read before this exercise

Reread the beginning of:

```text
Validating Input
```

Focus on empty input.

## Skill being gained

He is learning the first validation rule: do not assume the user typed something useful.

## Do not type yet

Predict:

```text
What should happen if the user types app01?
What should happen if the user presses Enter?
Should the error go to stdout or stderr?
```

## Create the script

```bash
cd ~/tlcl-ch28-input

cat > validate-empty.sh <<'SCRIPT'
#!/usr/bin/env bash

read -r -p 'Enter hostname > ' hostname

if [[ -z "$hostname" ]]; then
    echo 'Error: hostname must not be empty.' >&2
    exit 1
fi

printf 'hostname=<%s>\n' "$hostname"
SCRIPT

bash validate-empty.sh
echo "exit status=$?"

bash validate-empty.sh
echo "exit status=$?"
```

For the second run, press Enter with no input.

## Explain-back

Answer:

1. What does `-z` test?
2. Why quote `"$hostname"`?
3. Why use `>&2` for the error?
4. Why use `exit 1`?

---

# Exercise 2: Validate a menu choice

## Section to read before this exercise

Read the `Menus` section.

Then reread the part of `Validating Input` that uses tests and regex.

## Skill being gained

He is learning to accept only known choices.

## Do not type yet

Write the rules in English:

```text
Valid choices:
Invalid choices:
Regex or test I will use:
Action for invalid input:
```

For this exercise, valid choices are:

```text
0, 1, 2, 3
```

## Create the script

```bash
cat > menu-validate.sh <<'SCRIPT'
#!/usr/bin/env bash

cat <<'MENU'
Please Select:
1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
MENU

read -r -p 'Enter selection [0-3] > ' selection

if [[ ! "$selection" =~ ^[0-3]$ ]]; then
    echo "Error: invalid selection '$selection'" >&2
    exit 1
fi

printf 'Valid selection: <%s>\n' "$selection"
SCRIPT

bash menu-validate.sh
```

Try:

```text
1
0
4
banana
empty input
1 2
```

## Explain-back

Answer:

1. What does `^[0-3]$` mean?
2. Why are `^` and `$` important?
3. Why would `[0-3]` alone be weaker?
4. Why is `1 2` invalid?
5. What branch runs for bad input?

---

# Exercise 3: Menu with actions using `if`

## Section to read before this exercise

Reread `Menus`.

Also review Chapter 27 if needed:

```text
if Statements
Exit Status
Using test
A More Modern Version of test
```

## Skill being gained

He is learning that a menu is input plus branching.

## Do not type yet

Before typing, complete this table:

| Input | Expected action |
|---|---|
| 1 | |
| 2 | |
| 3 | |
| 0 | |
| banana | |

## Create the script

```bash
cat > simple-menu.sh <<'SCRIPT'
#!/usr/bin/env bash

cat <<'MENU'
Please Select:
1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
MENU

read -r -p 'Enter selection [0-3] > ' selection

if [[ ! "$selection" =~ ^[0-3]$ ]]; then
    echo "Error: invalid selection '$selection'" >&2
    exit 1
fi

if [[ "$selection" == "1" ]]; then
    echo 'System Information'
    hostname
    date
    uname -a
elif [[ "$selection" == "2" ]]; then
    echo 'Disk Space'
    df -h
elif [[ "$selection" == "3" ]]; then
    echo 'Home Space Utilization'
    du -sh "$HOME" 2>/dev/null
else
    echo 'Quit.'
fi
SCRIPT

bash simple-menu.sh
```

Test every valid branch and at least two invalid branches.

## Explain-back

Answer:

1. Where does input enter the script?
2. Where is input validated?
3. Where does branching happen?
4. Why should validation come before the actions?
5. Which previous chapters does this script reuse?

---

# Exercise 4: Build `interactive-report.sh`

## Section to read before this exercise

Reread:

```text
Validating Input
Menus
Summing Up
```

Ask what this chapter adds to the earlier report-generator project.

## Skill being gained

He is learning to combine:

```text
functions from Chapter 26
if from Chapter 27
read from Chapter 28
validation from Chapter 28
```

## Do not type yet

Design first:

```text
Script job:
Menu choices:
Valid input:
Invalid input:
Functions needed:
How I will test each branch:
```

## Create the script

```bash
cat > interactive-report.sh <<'SCRIPT'
#!/usr/bin/env bash

report_system_info () {
    echo '<h2>System Information</h2>'
    echo '<pre>'
    hostname
    date
    uname -a
    echo '</pre>'
}

report_disk_space () {
    echo '<h2>Disk Space</h2>'
    echo '<pre>'
    df -h
    echo '</pre>'
}

report_home_space () {
    echo '<h2>Home Space Utilization</h2>'
    echo '<pre>'
    du -sh "$HOME" 2>/dev/null
    echo '</pre>'
}

print_html_page () {
    local section="$1"

    echo '<html>'
    echo '<head><title>Interactive Report</title></head>'
    echo '<body>'
    echo '<h1>Interactive Report</h1>'

    if [[ "$section" == "1" ]]; then
        report_system_info
    elif [[ "$section" == "2" ]]; then
        report_disk_space
    elif [[ "$section" == "3" ]]; then
        report_home_space
    fi

    echo '</body>'
    echo '</html>'
}

cat <<'MENU'
Please Select Report Section:
1. System Information
2. Disk Space
3. Home Space Utilization
0. Quit
MENU

read -r -p 'Enter selection [0-3] > ' selection

if [[ ! "$selection" =~ ^[0-3]$ ]]; then
    echo "Error: invalid selection '$selection'" >&2
    exit 1
fi

if [[ "$selection" == "0" ]]; then
    echo 'No report generated.'
    exit 0
fi

print_html_page "$selection"
SCRIPT
```

Test syntax:

```bash
bash -n interactive-report.sh
```

Run and save output:

```bash
bash interactive-report.sh > report.html
```

Choose `1`, then inspect:

```bash
grep -E '<h1>|<h2>' report.html
```

Repeat for choices `2` and `3`.

For invalid input, do not redirect stdout at first:

```bash
bash interactive-report.sh
```

Try:

```text
4
banana
empty input
1 2
```

## Explain-back

Answer:

1. Why does `print_html_page` take one argument?
2. Why is `section` local?
3. Why validate before calling `print_html_page`?
4. Why does choice `0` exit before generating HTML?
5. Why is this not yet a repeated menu?
6. Which later chapter will make repeated menus easier?

---

# Exercise 5: Make a test checklist

## Skill being gained

He is learning that interactive scripts require multiple test cases.

Create this file:

```bash
cat > ch28-test-checklist.md <<'SCRIPT'
# Chapter 28 Test Checklist

## Valid inputs

- [ ] 0 quits without report
- [ ] 1 creates system-information report
- [ ] 2 creates disk-space report
- [ ] 3 creates home-space report

## Invalid inputs

- [ ] empty input rejected
- [ ] 4 rejected
- [ ] banana rejected
- [ ] 1 2 rejected
- [ ] punctuation rejected

## Explanation

- [ ] I can explain where input enters the script.
- [ ] I can explain where validation happens.
- [ ] I can explain why the regex uses ^ and $.
- [ ] I can explain why errors go to stderr.
- [ ] I can explain why this menu does not loop yet.
SCRIPT
```

Fill it in after testing.

---

# Day 2 final concept questions

Answer in writing:

1. What is the difference between reading input and validating input?
2. Why is empty input often invalid?
3. Why should invalid input usually stop the script before actions happen?
4. What makes `^[0-3]$` more precise than `[0-3]`?
5. Why is `read -r` a good default?
6. Why should user input usually be quoted when expanded?
7. What is a menu-driven script?
8. Why is this chapter a bridge between simple scripts and real tools?
9. What is still awkward without loops?
10. What should Chapter 29 add?

---

# Final Feynman explain-back

Explain Chapter 28 to a younger student:

```text
A script can ask the user for input using read.
The answer is stored in a variable.
But the script should not trust the answer.
It should check whether the answer is empty, wrong, or unsafe.
A menu is a controlled way to ask the user what action to run.
```

Then explain this command:

```bash
read -r -p 'Enter selection [0-3] > ' selection
```

Use this structure:

```text
read:
-r:
-p:
prompt text:
selection:
```

Then explain this validation:

```bash
[[ ! "$selection" =~ ^[0-3]$ ]]
```

Use this structure:

```text
[[ ... ]]:
!:
"$selection":
=~:
^:
[0-3]:
$:
```

If he cannot explain each part, he should review Day 2 before moving on.
