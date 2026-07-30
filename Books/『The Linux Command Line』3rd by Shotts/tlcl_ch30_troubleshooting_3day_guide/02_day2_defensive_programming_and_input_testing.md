# TLCL Chapter 30: Troubleshooting

This guide is for Chapter 30, **Troubleshooting**, from William Shotts's *The Linux Command Line*.

The goal is not to memorize debugging tricks.

The goal is to build this habit:

```text
Observe the failure.
State what should have happened.
Make one hypothesis.
Use one test.
Inspect evidence.
Change one thing.
Retest.
Explain what happened.
```

Feynman analogy:

```text
Debugging is like fixing a lamp.
A careless person shakes it and hopes it works.
A disciplined thinker asks:
Is there power? Is the bulb good? Is the switch working? Is the wire broken?
One test at a time.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
```

Disciplined rule:

```text
Do not randomly edit a broken script.
Write down what you expected, what actually happened, and what evidence you used.
```

---
# Day 2: Defensive Programming and Input Testing

## Read before exercises

Read these Chapter 30 sections:

```text
Defensive Programming
set -e, set -u, and set -o pipefail
ShellCheck Is Your Friend
Watch Out for Filenames
Verifying Input
Testing
Test Cases
```

## What he should gain from this reading

He should gain this idea:

```text
Good scripts do not merely work when input is perfect.
Good scripts defend themselves against bad input, missing files, strange filenames, and failed commands.
```

He is learning to think like this:

```text
What can go wrong?
How will I detect it?
What should the script do safely?
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
Defensive programming is like checking the bridge before driving a truck over it.
You do not assume the bridge is safe because you want it to be safe.
You inspect it.
```

In shell scripts, the “bridge” may be:

```text
a filename
a directory
a user answer
a command exit status
a variable that might be empty
a pipeline where one stage failed
```

---

# After reading: concept questions

Answer without looking back:

1. What is defensive programming?
2. Why should a script verify input before acting?
3. What does `set -e` try to do?
4. Why can `set -e` be helpful but not a substitute for thinking?
5. What does `set -u` do with unset variables?
6. Why can pipeline failures be hidden?
7. What does `set -o pipefail` help reveal?
8. What kinds of problems can ShellCheck find?
9. Why are filenames dangerous in shell scripts?
10. What is a test case?
11. Why should he test both good and bad input?

---

# Exercise 1: Verify input before action

## Skill being gained

He is learning to check preconditions.

## Predict before typing

Answer:

```text
What should the script do if the file exists?
What should it do if the file does not exist?
What should it do if no filename is provided?
```

## Create script

```bash
cd ~/tlcl-ch30-troubleshooting

cat > show-file.sh <<'EOF'
#!/usr/bin/env bash

filename=$1

if [[ -f "$filename" ]]; then
    head "$filename"
else
    echo "Error: '$filename' is not a regular file" >&2
    exit 1
fi
EOF
```

Run test cases:

```bash
bash show-file.sh /etc/passwd
bash show-file.sh /no/such/file
echo $?
bash show-file.sh
echo $?
```

## Explain-back

Answer:

1. What happens when `$1` is empty?
2. Is the error message clear enough?
3. Does the script exit with failure when input is bad?
4. Why is `>&2` used for error messages?

## Improve it

Rewrite with explicit missing-argument handling:

```bash
cat > show-file.sh <<'EOF'
#!/usr/bin/env bash

filename=$1

if [[ -z "$filename" ]]; then
    echo "Usage: show-file.sh FILE" >&2
    exit 2
fi

if [[ -f "$filename" ]]; then
    head "$filename"
else
    echo "Error: '$filename' is not a regular file" >&2
    exit 1
fi
EOF
```

Retest:

```bash
bash show-file.sh /etc/passwd
bash show-file.sh /no/such/file
bash show-file.sh
```

---

# Exercise 2: Use safer defaults and observe `set -u`

## Skill being gained

He is learning how unset variables create hidden bugs.

## Predict before typing

Answer:

```text
What happens when a script uses a variable that was never assigned?
How could that become dangerous?
```

## Create two scripts

```bash
cat > unset-unsafe.sh <<'EOF'
#!/usr/bin/env bash

echo "The target is: $target"
EOF

cat > unset-safer.sh <<'EOF'
#!/usr/bin/env bash
set -u

echo "The target is: $target"
EOF
```

Run:

```bash
bash unset-unsafe.sh
bash unset-safer.sh
```

## Explain-back

Answer:

1. What did the unsafe script print?
2. What did the safer script do?
3. Why can an empty variable be dangerous in path operations?
4. Why is `set -u` useful during learning?
5. Why is `set -u` not a replacement for clear variable initialization?

---

# Exercise 3: Pipeline failure and `pipefail`

## Skill being gained

He is learning that a pipeline may look successful even when an early command failed.

## Predict before typing

Answer:

```text
In a pipeline, whose exit status does Bash normally report?
What could go wrong if an early command fails?
```

## Create test script

```bash
cat > pipefail-test.sh <<'EOF'
#!/usr/bin/env bash

printf 'Without pipefail: '
grep 'root' /no/such/file | wc -l
echo "exit=$?"

set -o pipefail

printf 'With pipefail: '
grep 'root' /no/such/file | wc -l
echo "exit=$?"
EOF

bash pipefail-test.sh
```

## Explain-back

Answer:

1. What did `wc -l` output?
2. Did `grep` fail?
3. What changed after `set -o pipefail`?
4. Why does this matter in scripts that process logs or config files?

---

# Exercise 4: Filename discipline

## Skill being gained

He is learning to protect against spaces and leading hyphens in filenames.

## Setup

```bash
mkdir -p filename-lab
cd filename-lab
: > 'normal.txt'
: > 'two words.txt'
: > '--danger.txt'
cd ..
```

## Predict before typing

Answer:

```text
Why can spaces break unquoted variables?
Why can a filename beginning with - be mistaken for an option?
What does -- usually mean?
```

## Run safe previews

```bash
cd filename-lab
printf '<%s>
' *.txt

for file in *.txt; do
    printf 'Would inspect: <%s>
' "$file"
done

for file in *.txt; do
    ls -l -- "$file"
done
cd ..
```

## Explain-back

Answer:

1. Why is `"$file"` quoted?
2. Why use `--` before `"$file"`?
3. What could happen without those habits?

---

# Exercise 5: Test cases

## Skill being gained

He is learning to prove the script works in multiple cases.

Create a test-case list for `show-file.sh`:

```text
Case 1: existing regular file
Command: bash show-file.sh /etc/passwd
Expected: prints first lines, exit 0

Case 2: missing file
Command: bash show-file.sh /no/such/file
Expected: error message, nonzero exit

Case 3: no argument
Command: bash show-file.sh
Expected: usage message, exit 2

Case 4: filename with spaces
Command: bash show-file.sh "filename-lab/two words.txt"
Expected: no splitting, script handles it
```

Run them.

For each, record:

```text
Expected:
Actual:
Exit status:
Pass/fail:
Fix needed:
```

---

# Day 2 finish standard

He is done only if he can say:

```text
I can verify input before action.
I can send error messages to stderr.
I can use exit statuses deliberately.
I know why set -u and pipefail can reveal hidden bugs.
I can protect filenames with quotes and --.
I can create test cases before claiming a script works.
```
