# TLCL Chapter 32: Positional Parameters
# Day 2: Handling Positional Parameters En Masse

## Read before exercises

Read the Chapter 32 section:

```text
Handling Positional Parameters en Masse
```

## What he should gain from this reading

This day is about **many arguments**.

Key items:

```text
$*
$@
"$*"
"$@"
shift
```

Most important practical habit:

```text
Use "$@" when you need to loop over all arguments safely.
```

---

# After reading: concept questions

Answer without looking back:

1. What is the difference between handling one positional parameter and handling many?
2. What does `shift` do?
3. Why can `shift` be useful in a loop?
4. What is the practical danger of unquoted `$@`?
5. Why is `"$@"` usually the safest way to pass or loop over arguments?
6. Why can filenames with spaces expose bad argument handling?
7. What is the difference between the arguments the user typed and the arguments the script receives?

Do not continue until these are answered.

---

# Exercise 1: Compare `$*`, `$@`, `"$*"`, and `"$@"`

## Skill being gained

He is learning that small quoting differences change argument behavior.

## Do not type yet

Predict what happens when the script receives:

```bash
one "two words" three
```

Question:

```text
Will "two words" stay together or get split apart?
```

## Create the script

```bash
cd ~/tlcl-ch32-positional-parameters

cat > compare-all-args.sh <<'EOF'
#!/usr/bin/env bash

echo 'Loop over $*'
for arg in $*; do
    printf '<%s>
' "$arg"
done

echo

echo 'Loop over $@'
for arg in $@; do
    printf '<%s>
' "$arg"
done

echo

echo 'Loop over "$*"'
for arg in "$*"; do
    printf '<%s>
' "$arg"
done

echo

echo 'Loop over "$@"'
for arg in "$@"; do
    printf '<%s>
' "$arg"
done
EOF

chmod +x compare-all-args.sh
./compare-all-args.sh one "two words" three
```

## Explain-back

Answer:

1. Which version preserved `two words` as one argument?
2. Which version treated all arguments as one big string?
3. Which versions were unsafe because they allowed word splitting?
4. Why is `"$@"` usually the correct choice?

Write the rule:

```text
When I want each original argument preserved separately, I use "$@".
```

---

# Exercise 2: Use `shift` to process arguments one at a time

## Skill being gained

He is learning that `shift` moves the argument list forward.

## Read before exercise

Reread the part of the chapter that explains `shift`.

## Do not type yet

Predict this sequence:

```text
Initial arguments: alpha beta gamma
Before shift: $1 = ?
After one shift: $1 = ?
After two shifts: $1 = ?
```

## Create the script

```bash
cat > shift-demo.sh <<'EOF'
#!/usr/bin/env bash

while [[ $# -gt 0 ]]; do
    echo "Current first argument: $1"
    echo "Arguments remaining: $#"
    shift
    echo
 done
EOF

chmod +x shift-demo.sh
./shift-demo.sh alpha beta gamma
```

Note: if the indentation before `done` bothers you, fix it. The shell cares about the keyword, but humans care about readable structure.

## Explain-back

Answer:

1. What does `shift` remove?
2. What happens to old `$2` after `shift`?
3. Why does `$#` get smaller?
4. What would happen if the loop forgot to call `shift`?

---

# Exercise 3: Prefer `"$@"` for simple loops

## Skill being gained

He is learning that many cases do not require `shift`; `for arg in "$@"` is clearer.

## Do not type yet

State the job:

```text
Print each argument exactly as the user passed it.
```

Which is clearer?

```bash
while [[ $# -gt 0 ]]; do ... shift; done
```

or:

```bash
for arg in "$@"; do ... done
```

## Create the script

```bash
cat > list-args-safely.sh <<'EOF'
#!/usr/bin/env bash

count=1
for arg in "$@"; do
    printf 'Argument %d: <%s>
' "$count" "$arg"
    ((count++))
done
EOF

chmod +x list-args-safely.sh
./list-args-safely.sh alpha "beta gamma" "*.txt"
```

## Explain-back

Answer:

1. Why did `"beta gamma"` stay one argument?
2. Why did `"*.txt"` not expand into filenames?
3. What does `((count++))` do?
4. Why are both `$count` and `$arg` quoted in `printf`?

---

# Exercise 4: Upgrade report script to accept multiple sections

## Skill being gained

He is learning to loop through requested sections.

## Command-line contract

Before typing, write this:

```text
Command:
./lab-report.sh TITLE SECTION...

TITLE:
first argument

SECTION:
one or more of: system, disk, home
```

Example:

```bash
./lab-report.sh "App01 Report" system disk
```

## Create the script

```bash
cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

usage () {
    echo "Usage: $0 REPORT_TITLE SECTION..." >&2
    echo "Sections: system disk home" >&2
}

if [[ $# -lt 2 ]]; then
    usage
    exit 1
fi

report_title=$1
shift

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

report_footer () {
    echo "</body>"
    echo "</html>"
}

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
        *)
            echo "Unknown section: $section" >&2
            usage
            exit 1
            ;;
    esac
done

report_footer
EOF

chmod +x lab-report.sh
```

## Test cases

```bash
./lab-report.sh
./lab-report.sh "Only Title"
./lab-report.sh "App01 Report" system > report.html
./lab-report.sh "App01 Report" system disk home > report.html
./lab-report.sh "App01 Report" badsection > report.html
```

Inspect:

```bash
grep -E '<h1>|<h2>' report.html
```

## Explain-back

Answer:

1. Why does the script save `report_title=$1` before `shift`?
2. After `shift`, what does `"$@"` contain?
3. Why is `for section in "$@"` safer than `for section in $@`?
4. Why does the unknown-section branch exit with failure?
5. Why is `case` clearer than many `if` statements here?

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
shift removes the current first positional parameter.
"$@" preserves each original argument separately.
Unquoted $@ can split arguments and cause bugs.
A script that accepts many arguments must define what each one means.
```

He must also be able to explain why this is usually wrong:

```bash
for file in $@; do
    echo "$file"
done
```

And why this is usually better:

```bash
for file in "$@"; do
    echo "$file"
done
```
