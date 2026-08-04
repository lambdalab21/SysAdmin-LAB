# TLCL Chapter 30: Troubleshooting
Working directory:

```bash
mkdir -p ~/tlcl-ch30-troubleshooting
cd ~/tlcl-ch30-troubleshooting
```
# Day 3: Debugging, Tracing, and Final Lab

## Read before exercises

Read these Chapter 30 sections:

```text
Debugging
Finding the Problem Area
Tracing
Examining Values During Execution
Summing Up
```

# After reading: concept questions

Answer without looking back:

1. What does it mean to find the problem area? Narrow the bug down to the smallest code section that causes it. 
2. Why should a bug be isolated before editing? Avoid changing unrelated code and creating new bugs. 
3. What does `bash -x script.sh` show? Each command as it runs, with expansions. 
4. Why is tracing sometimes too noisy? Long scripts produce outputs that are hard to read. 
5. How can `set -x` and `set +x` trace only part of a script? Turn tracing on only around the suspicious lines. 
6. Why should variable values be inspected during execution? To see the real values the script is using. 
7. Why is `printf` often better than vague `echo` debugging? printf shows exact content and spaces clearly. 
8. Why should each fix be followed by retesting? To confirm the fix worked and nothing else broke. 
9. What is the danger of making several changes before retesting? You cannot tell which change edited what. 
10. What makes a useful debugging note? Clear expected/actual behavior and evidence. 

---

# Exercise 1: Trace a simple script
## Create script

```bash
cd ~/tlcl-ch30-troubleshooting

cat > trace-demo.sh <<'EOF'
#!/usr/bin/env bash

name="linuxbox"
count=3

echo "Hello, $name"
echo "Count is $count"
EOF
```

Run normally:

```bash
bash trace-demo.sh
```

Run with trace:

```bash
bash -x trace-demo.sh
```

## Explain-back

Answer:

1. What extra information did `bash -x` show? Each command line before execution. 
2. Did it show `$name` literally or expanded? Expanded values. 
3. How can this help with debugging? Shows what the shell actually ran. 
4. Why might this be too noisy in a long script? Too many lines to scan in a large script. 

---

# Exercise 2: Trace only the suspicious area
## Create script

```bash
cat > selective-trace.sh <<'EOF'
#!/usr/bin/env bash

file="/etc/passwd"
pattern="root"

echo "Starting check"

set -x
grep "$pattern" "$file" | head -n 1
set +x

echo "Finished check"
EOF

bash selective-trace.sh
```

## Explain-back

Answer:

1. Which lines were traced? The `grep | head` line. 
2. Which lines were not traced? The `echo` lines outside `set -x / set +x`
3. Why is selective tracing better than tracing everything? Focuses attention and reduces noise. 
4. What command actually ran after variables expanded? `grep root /etc/passwd | head -n 1`

---

# Exercise 3: Examine values during execution
## Create script

```bash
cat > value-debug.sh <<'EOF'
#!/usr/bin/env bash

input="  report file.txt  "
empty=""

printf 'input=<%s>
' "$input"
printf 'empty=<%s>
' "$empty"
printf 'home=<%s>
' "$HOME"
EOF

bash value-debug.sh
```

## Explain-back

Answer:

1. How can angle brackets reveal spaces? Spaces appear between the brackets. 
2. How can they reveal empty variables? Empty variables show as <>. 
3. Why is this useful before using a variable as a filename? It prevents using a blank or space-filled name a a path. 

---

# Exercise 4: Final debugging lab
## Create intentionally flawed script

```bash
cat > lab-check.sh <<'EOF'
#!/usr/bin/env bash

# lab-check.sh - intentionally flawed system check script

target_dir=$1
pattern=$2

report_header () {
    echo "Lab Check Report"
    echo "================"
}

check_directory () {
    if [[ -d $target_dir ]]; then
        echo "Directory: $target_dir"
    else
        echo "Directory not found: $target_dir"
    fi
}

search_logs () {
    echo "Searching for pattern: $pattern"
    grep -R $pattern $target_dir | head
}

report_header
check_directory
search_logs
EOF
```

## Do not run yet

Before running, answer:

```text
What arguments does this script expect? Expects: directory and pattern. 
What should happen with good input? Good input: header + directory line + matching log lines. 
What should happen with missing input? Missing input: empty vars, broken searches 
What variables are unquoted? Unquoted: $target_dir, $pattern. 
What commands might fail? Missing directories, missing arguments, or bad grep. 
What filenames or patterns might be dangerous? Spaces in names or patterns starting with -. 
```

## Run test cases

Create a safe lab:

```bash
mkdir -p sample-logs
cat > sample-logs/app.log <<'EOF'
INFO startup complete
ERROR disk almost full
WARN retrying request
EOF

cat > 'sample-logs/two words.log' <<'EOF'
ERROR filename contains spaces
EOF
```

Run:

```bash
bash -n lab-check.sh
bash lab-check.sh sample-logs ERROR
bash lab-check.sh sample-logs
bash lab-check.sh /no/such/dir ERROR
bash -x lab-check.sh sample-logs ERROR
```

## Improve the script

After diagnosing, replace it with this safer version:

```bash
cat > lab-check.sh <<'EOF'
#!/usr/bin/env bash

# lab-check.sh - safer system check script

set -u
set -o pipefail

target_dir=${1:-}
pattern=${2:-}

usage () {
    echo "Usage: lab-check.sh DIRECTORY PATTERN" >&2
}

report_header () {
    echo "Lab Check Report"
    echo "================"
}

verify_input () {
    if [[ -z "$target_dir" || -z "$pattern" ]]; then
        usage
        exit 2
    fi

    if [[ ! -d "$target_dir" ]]; then
        echo "Error: '$target_dir' is not a directory" >&2
        exit 1
    fi
}

check_directory () {
    echo "Directory: $target_dir"
}

search_logs () {
    echo "Searching for pattern: $pattern"
    grep -R -- "$pattern" "$target_dir" | head
}

verify_input
report_header
check_directory
search_logs
EOF
```

Retest:

```bash
bash -n lab-check.sh
bash lab-check.sh sample-logs ERROR
bash lab-check.sh sample-logs
bash lab-check.sh /no/such/dir ERROR
bash lab-check.sh 'sample-logs' 'disk almost'
```

## Explain-back

Answer:

1. Why use `${1:-}` instead of `$1` with `set -u`? Avoids unbound-variable errors when args are missing. 
2. Why validate before searching? Fail fast with a clear message instead of cryptic failures. 
3. Why quote `"$target_dir"` and `"$pattern"`? Prevents word-splitting and globbing. 
4. Why use `--` before grep arguments? Stops patterns starting with - from being treated as options. 
5. Why is `usage` a function? Reusable, keeps main flow clean. 
6. Which failures now produce clear messages? Missing arguments and non-directory paths. 
7. Which test cases prove the script is safer? No argument, missing directory, and spaced-filename cases. 

---

# Final concept questions

Answer in writing:

1. What is the difference between syntax, expansion, and logical errors? Syntax = parse error. Expansion = wrong values after $/*; logical = runs but does the wrong thing.
2. Why should `bash -n` be run before testing a changed script? Catches syntax errors before runtime. 
3. What does `bash -x` show? Commands as executed with expansions. 
4. Why does quoting variables prevent many bugs? It stops word-splitting and globbing. 
5. Why can filenames be dangerous? Spaces, leading -, and special characters break unquoted use. 
6. What does `--` protect against? Filenames or patterns that look like options. 
7. Why should error messages go to stderr? Keeps them separate from normal output. 
8. Why should bad input be tested intentionally? Proves that the script defends itself. 
9. Why is one change at a time better than many changes at once? You can isolate which change fixed the bug. 
10. What makes a debugging explanation convincing? Specific expected/actual evidence tied to a cause and fix. 