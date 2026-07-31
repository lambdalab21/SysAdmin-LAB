# TLCL Chapter 30: Troubleshooting
Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
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
