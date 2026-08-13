# TLCL Chapter 30: Troubleshooting
Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
```
---
# Day 2: Defensive Programming and Input Testing

# After reading: concept questions

Answer without looking back:

1. What is defensive programming? Writing code that anticipates and safely handles bad input and edge cases. 
2. Why should a script verify input before acting? To avoid acting on invalid data and producing incorrect/dangerous results. 
3. What does `set -e` try to do? Exit the script if any command fails. 
4. Why can `set -e` be helpful but not a substitute for thinking? It misses some failure modes and can hide useful logic. 
5. What does `set -u` do with unset variables? It treats unset variables and aborts. 
6. Why can pipeline failures be hidden? Only the last command's exit status is seen by default. 
7. What does `set -o pipefail` help reveal? Pipeline failure from any command in the pipe. 
8. What kinds of problems can ShellCheck find? Unquoted variables, common bugs, style problems and possible logic mistakes. 
9. Why are filenames dangerous in shell scripts? Spaces, leading dashes, and special characters that can break unquoted expansions. 
10. What is a test case? A specific input scenario with expected outputs and exit status.

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

1. What happens when `$1` is empty? It's treated as empty. The -f test fails and the error path runs. 
2. Is the error message clear enough? Mostly. A usage message is clearer. 
3. Does the script exit with failure when input is bad? Yes. 
4. Why is `>&2` used for error messages? It sends errors to stderr so that they stay separate from standard outputs. 

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
basg cat > unset-unsafe.sh <<'EOF'
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

1. What did the unsafe script print? "The target is:" with an empty value. 
2. What did the safer script do? Aborted with "unbound variable"
3. Why can an empty variable be dangerous in path operations? It can expend to dangerous paths like / or empty arguments. 
4. Why is `set -u` useful during learning? Surface typos and missing initialization. 
5. Why is `set -u` not a replacement for clear variable initialization? You still need to set variables with correct values. 

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

1. What did `wc -l` output? 0
2. Did `grep` fail? Yes. 
3. What changed after `set -o pipefail`? The pipeline's exit status became nonzero. 
4. Why does this matter in scripts that process logs or config files? Scripts ust notice missing/corrupt files instead of continuing. 

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

1. Why is `"$file"` quoted? It prevents word-splitting and globbing.
2. Why use `--` before `"$file"`? It stops the filename from being treated as an option. 
3. What could happen without those habits? Wrong files inspected, commands failing, or unexpected option errors. 