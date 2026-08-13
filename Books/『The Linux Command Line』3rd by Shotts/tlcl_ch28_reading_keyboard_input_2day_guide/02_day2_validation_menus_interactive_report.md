# Day 2: Validating Input, Menus, and an Interactive Report

## Read before exercises

Read these Chapter 28 sections:

```text
Validating Input
Menus
Summing Up
```

---

# After-reading concept questions

Answer without looking back:

1. Why must scripts validate input? Scripts must validate input because users can provide bad, empty, or malicious data. 
2. Why is validation especially important before destructive actions? Destructive actions can cause major damage if they're ran with invalid input. 
3. How can `[[ -z "$var" ]]` help validate input? [[ -z "$var" ]] checks if the variable is empty. 
4. How can regex help validate input? Regex can match patterns to ensure input format. 
5. Why should error messages often go to stderr? Error messages go to stderr so that they are separate from normal output
6. What is a menu-driven program? A menu-driven program presents options and acts based on user choices. 
7. What are valid menu choices in a simple numbered menu? Valid choices are usually 0 through 3. 
8. What should the script do when the user enters an invalid choice? Show an error and exit, or reprompt. 
9. Why should validation happen before action? To prevent running actions. 
10. How does Chapter 28 reuse ideas from Chapter 27? It reuses if statements. 

---

# Exercise 1: Reject empty input

## Section to read before this exercise

Reread the beginning of:

```text
Validating Input
```

Focus on empty input.

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

1. What does `-z` test? It tests if a string is empty. 
2. Why quote `"$hostname"`? To prevent word splitting and globbing.
3. Why use `>&2` for the error?Directs error outputs from normal stdout. 
4. Why use `exit 1`? Signals failure to the shell process. 

---

# Exercise 2: Validate a menu choice

## Section to read before this exercise

Read the `Menus` section.

Then reread the part of `Validating Input` that uses tests and regex.

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

1. What does `^[0-3]$` mean? Matches a single character that is exactly 0, 1, 2, or 3. 
2. Why are `^` and `$` important? ^ anchors to start of string; $ to end - ensures entire input matches, no extras. 
3. Why would `[0-3]` alone be weaker? [0-3] alone matches anywhere in the string. 
4. Why is `1 2` invalid? Contains space and extra characters, doesn't match the full pattern. 
5. What branch runs for bad input? The if [[ ! ... ]] error branch runs and exits. 

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

1. Where does input enter the script? Via read -r -p ... selection. 
2. Where is input validated? Immediately after reading, with the regex test. 
3. Where does branching happen? In the if/elif/else chain after validation. 
4. Why should validation come before the actions? Prevents running potentially harmful commands with bad data. 
5. Which previous chapters does this script reuse? Chapter 27.

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

1. Why does `print_html_page` take one argument? To specify which report section to generate(0, 1, 2, or 3)
2. Why is `section` local? Keeps it scoped to the function, avoids polluting global namescapes. 
3. Why validate before calling `print_html_page`? Ensures only valid sections reach the HTML generation logic. 
4. Why does choice `0` exit before generating HTML? No report is needed. 
5. Why is this not yet a repeated menu? It runs once and exits. No loop to redisplay menu. 
6. Which later chapter will make repeated menus easier? Probably Chapter 29. 

---

# Exercise 5: Make a test checklist

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

1. What is the difference between reading input and validating input? Reading input gets raw user data; validating input checks it for accuracy, safety, and expected format before usage. 
2. Why is empty input often invalid? Empty inputs are often invalid because it can cause errors, unexpected behavior, or bypass intended logic in scripts. 
3. Why should invalid input usually stop the script before actions happen? Invalid inputs should stop the script early to avoid running commands with malicious data that could cause damage, errors, or security issues. 
4. What makes `^[0-3]$` more precise than `[0-3]`? It's more precise because ^ and $ anchor the match to the entire string. 
5. Why is `read -r` a good default? read -r is a good default because it prevents backslash interpretation, preserving literal input as it's typed. 