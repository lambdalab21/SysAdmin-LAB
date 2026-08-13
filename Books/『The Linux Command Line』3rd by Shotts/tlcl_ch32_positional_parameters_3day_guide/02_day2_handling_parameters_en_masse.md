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

1. What is the difference between handling one positional parameter and handling many? One value vs looping over / forwarding all of them. 
2. What does `shift` do? Removes $1 and shifts the rest left. 
3. Why can `shift` be useful in a loop? Processing arguments one by one until none of the mare left. 
4. What is the practical danger of unquoted `$@`? Word splitting and globbing break arguments with spaces. 
5. Why is `"$@"` usually the safest way to pass or loop over arguments? It keeps each original arguments intact. 
6. Why can filenames with spaces expose bad argument handling? Unquoted expansion splits them into multiple words. 
7. What is the difference between the arguments the user typed and the arguments the script receives? The shell may have already split or joined them before the script sees them. 

---

# Exercise 1: Compare `$*`, `$@`, `"$*"`, and `"$@"`

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

1. Which version preserved `two words` as one argument? "$0"
2. Which version treated all arguments as one big string? "$*"
3. Which versions were unsafe because they allowed word splitting? "$*" and "$0"
4. Why is `"$@"` usually the correct choice? It preserves each argument separately and intact. 

---

# Exercise 2: Use `shift` to process arguments one at a time

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

## Explain-back

Answer:

1. What does `shift` remove? The current $1. 
2. What happens to old `$2` after `shift`? It becomes the new $1. 
3. Why does `$#` get smaller? One argument was removed. 
4. What would happen if the loop forgot to call `shift`? Infinite loop on the same $1. 

---

# Exercise 3: Prefer `"$@"` for simple loops

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

1. Why did `"beta gamma"` stay one argument? Quotes keep it as one argument. 
2. Why did `"*.txt"` not expand into filenames? Quotes prevented glob expansions. 
3. What does `((count++))` do? Increments count by one. 
4. Why are both `$count` and `$arg` quoted in `printf`? Safe expansions without splitting or globbing. 

---

# Exercise 4: Upgrade report script to accept multiple sections

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
