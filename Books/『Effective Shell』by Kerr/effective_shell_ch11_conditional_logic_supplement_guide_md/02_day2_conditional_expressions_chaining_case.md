# Day 2: `[[ ]]`, Regex Tests, Command Chaining, and `case`

## Read before exercises

Read Kerr Ch. 11 sections:

```text
Combining Tests
Conditional Expressions
Using Regexes in a Conditional Expression
Chaining Commands
Case Statements
Updating the 'Common' Command
Summary
```

## What he should gain

He should gain a practical decision rule:

```text
Use [ ... ] for simple portable tests.
Use [[ ... ]] for Bash-friendly conditional expressions.
Use case when one value has several possible patterns.
Use && and || for short command chains, not complicated program logic.
```

This is mostly a TLCL refresh, but Kerr’s value is in making these tools feel like part of a small practical script.

---

# After reading: concept questions

Answer before exercises:

1. Why can combining tests with `[ ... -a ... ]` or `[ ... -o ... ]` be less desirable?
2. What are advantages of `[[ ... ]]` in Bash?
3. What does `=~` do inside `[[ ... ]]`?
4. Why should a regex often be stored in a variable before using `[[ string =~ regex ]]`?
5. What does `command1 && command2` mean?
6. What does `command1 || command2` mean?
7. Why can command chaining be useful interactively?
8. Why can too much chaining make scripts harder to read?
9. When is `case` clearer than many `elif` branches?
10. What is the purpose of the `*)` branch in `case`?

---

# Exercise 1: Convert a combined test into `[[ ... ]]`

## Skill being gained

He is learning to write clearer Bash conditionals.

## Predict before typing

For each value, decide whether it is in the 1980s:

```text
1979
1980
1985
1989
1990
```

## Create the script

```bash
cd ~/effective-shell-ch11-supplement

cat > decade-check.sh <<'EOF'
#!/usr/bin/env bash

year="$1"

if [[ -z "$year" ]]; then
    echo "Usage: $0 YEAR"
    exit 1
fi

if [[ "$year" -ge 1980 && "$year" -lt 1990 ]]; then
    echo "$year is in the 1980s."
else
    echo "$year is not in the 1980s."
fi
EOF

bash decade-check.sh
bash decade-check.sh 1979
bash decade-check.sh 1980
bash decade-check.sh 1985
bash decade-check.sh 1989
bash decade-check.sh 1990
```

## Explain-back

1. What does `-z "$year"` check?
2. Why are there two separate `if` statements?
3. What does `&&` mean inside `[[ ... ]]`?
4. What values prove the boundary is correct?
5. What bad input could still break or confuse this script?

Optional test:

```bash
bash decade-check.sh abc
```

Answer:

```text
What happened?
Should this script validate numeric input before numeric comparison?
```

---

# Exercise 2: Use regex in a conditional expression

## Skill being gained

He is learning to use regex for validation inside Bash.

## Read before exercise

Reread Kerr’s section on regex in conditional expressions.

## Predict before typing

Which should match this rule?

```text
A year must be exactly four digits.
```

Test values:

```text
1980
99
abcd
20240
2024a
```

## Improve the script

```bash
cat > decade-check-v2.sh <<'EOF'
#!/usr/bin/env bash

year="$1"
year_regex='^[0-9]{4}$'

if [[ -z "$year" ]]; then
    echo "Usage: $0 YEAR"
    exit 1
fi

if [[ ! "$year" =~ $year_regex ]]; then
    echo "Error: YEAR must be exactly four digits."
    exit 1
fi

if [[ "$year" -ge 1980 && "$year" -lt 1990 ]]; then
    echo "$year is in the 1980s."
else
    echo "$year is not in the 1980s."
fi
EOF

bash decade-check-v2.sh 1980
bash decade-check-v2.sh 99
bash decade-check-v2.sh abcd
bash decade-check-v2.sh 20240
bash decade-check-v2.sh 2024a
```

## Explain-back

1. What does `^[0-9]{4}$` mean?
2. Why are `^` and `$` important?
3. Why does validation happen before numeric comparison?
4. Why is the regex stored in a variable?
5. Is this regex a shell glob or a regex?

---

# Exercise 3: Command chaining with `&&` and `||`

## Skill being gained

He is learning short conditional execution.

## Read before exercise

Reread Kerr’s section on command chaining.

## Predict before typing

Predict which messages print:

```bash
true && echo success
false && echo success
true || echo failure
false || echo failure
```

## Run

```bash
true && echo "success after true"
false && echo "success after false"
true || echo "failure after true"
false || echo "failure after false"
```

## Practical example

```bash
mkdir -p data && echo "data directory is ready"
[ -f missing.txt ] || echo "missing.txt is not present"
```

## Explain-back

1. What does `&&` mean operationally?
2. What does `||` mean operationally?
3. Why is this useful interactively?
4. Why should long script logic usually use `if` instead?
5. What is risky about chaining commands you do not understand?

---

# Exercise 4: Use `case` when one value has several possible patterns

## Skill being gained

He is learning to choose `case` instead of a long chain of `elif` branches.

## Read before exercise

Reread Kerr’s `case` section.

## Predict before typing

For each input, predict which branch should run:

```text
y
Y
yes
no
n
maybe
empty input
```

## Create script

```bash
cat > confirm.sh <<'EOF'
#!/usr/bin/env bash

read -r -p "Continue? [y/n] " response

case "$response" in
    y|Y|yes|YES)
        echo "Confirmed."
        ;;
    n|N|no|NO)
        echo "Denied."
        ;;
    *)
        echo "Invalid response: '$response'"
        exit 1
        ;;
esac
EOF

bash confirm.sh
```

Run several times and test every branch.

## Explain-back

1. What value is `case` checking?
2. What does `|` mean inside a `case` pattern list?
3. What does `;;` do?
4. What does the `*)` branch catch?
5. Are these patterns regex or shell patterns?

---

# Exercise 5: Final lab — conditional history analyzer

## Skill being gained

He is learning to combine variables, conditionals, file tests, command chaining, and `case` into one practical script.

## Project purpose

Create a local version of a `common`-style command.

It should:

```text
accept a shell name: bash or zsh
choose the likely history file
check whether the file exists and is readable
print the most common commands
reject unsupported shell names clearly
```

## Do not type yet

Fill this out:

```text
Script name:
Argument required:
Allowed values:
For each value, what history file should be checked?
What file test is needed?
What should happen if the file is missing?
What should happen if the shell name is unsupported?
```

## Create sample data

Use sample files so the exercise is safe and predictable:

```bash
cat > data/bash_history <<'EOF'
ls
cd project
git status
ls
git status
git status
cat notes.txt
EOF

cat > data/zsh_history <<'EOF'
: 1700000000:0;ls
: 1700000001:0;cd project
: 1700000002:0;git status
: 1700000003:0;git status
: 1700000004:0;vim file.txt
EOF
```

## Write the script

```bash
cat > common-local.sh <<'EOF'
#!/usr/bin/env bash

shell_name="$1"
command_count=5
history_file=""

if [[ -z "$shell_name" ]]; then
    echo "Usage: $0 bash|zsh"
    exit 1
fi

case "$shell_name" in
    bash)
        history_file="data/bash_history"
        ;;
    zsh)
        history_file="data/zsh_history"
        ;;
    *)
        echo "Unsupported shell: $shell_name"
        exit 1
        ;;
esac

if [[ ! -r "$history_file" ]]; then
    echo "History file is missing or not readable: $history_file"
    exit 1
fi

if [[ "$shell_name" == "bash" ]]; then
    sort "$history_file" \
        | uniq -c \
        | sort -nr \
        | head -n "$command_count"
elif [[ "$shell_name" == "zsh" ]]; then
    sed 's/^.*;//' "$history_file" \
        | sort \
        | uniq -c \
        | sort -nr \
        | head -n "$command_count"
fi
EOF
```

## Test

```bash
bash common-local.sh
bash common-local.sh bash
bash common-local.sh zsh
bash common-local.sh fish
mv data/bash_history data/bash_history.bak
bash common-local.sh bash
mv data/bash_history.bak data/bash_history
```

## Explain-back

1. Which conditional form was used for choosing the shell name?
2. Which conditional form was used for checking file readability?
3. Why is `case` better than several `elif` branches here?
4. Why does the script validate before processing?
5. What pipeline is used for Bash history?
6. What extra cleanup is needed for the Zsh-style sample history?
7. Which test cases prove the script works?
8. What would you improve next?

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
I can choose between [ ], [[ ]], command chaining, and case.
I understand why [[ ]] is useful in Bash scripts.
I can validate input before using it.
I can use regex inside [[ ]] without confusing it with globs.
I can use case for multiple known patterns.
I can test every branch of a script deliberately.
```
