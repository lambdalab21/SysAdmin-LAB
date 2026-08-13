# TLCL Chapter 32: Positional Parameters

# Day 3: A More Complete Application and Final Lab

## Read before exercises

Read the Chapter 32 sections:

```text
A More Complete Application
Summing Up
```

## What he should gain from this reading

He should gain this idea:

```text
A useful script has a command-line interface, argument checks, useful error messages, and predictable behavior.
```

---
# After reading: concept questions

Answer without looking back:

1. What makes a script feel like a real command-line application? A real command-line app has a clear interface, argument checks, useful error messages, and predictable behavior. 
2. Why should a script have a usage function? To print clear help text showing how to use the script. 
3. Why should invalid input fail early? So bad input is caught immediately and the script doesn't produce partial or wrong results. 
4. Why is it better to test many argument combinations than one happy path? To catch edge cases and ensure robust behavior beyond the happy path. 
5. What does `"$@"` preserve?  All positional parameters as separate words. 
6. What does `shift` change? It removes the first positional parameter and shifts the rest to the left. 
7. Why should error messages usually go to `stderr`? So that they don't get mixed into normal output. 
8. Why should a script exit nonzero after argument errors? So that the caller can detect failure. 

---

# Exercise 1: Write the command contract before the script

Answer:
1. Which arguments are required? REPORT_TITLE and at least one section. 
2. Which argument comes first? REPORT_TITLE. 
3. Can sections appear in any order?No. 
4. What should `--help` do? Print usage information and exit. 
5. Should `--help` be success or failure? Success. 

---

# Exercise 2: Build the complete version

## Create the script

```bash
cd ~/tlcl-ch32-positional-parameters

cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

usage () {
    cat <<USAGE
Usage: $0 REPORT_TITLE SECTION...
       $0 --help

Generate a simple HTML lab report.

Sections:
  system   include hostname, date, and kernel information
  disk     include disk-space information
  home     include home-directory size
  all      include all report sections
USAGE
}

fail () {
    echo "Error: $1" >&2
    echo >&2
    usage >&2
    exit 1
}

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

report_all () {
    report_system_info
    report_disk_space
    report_home_space
}

report_footer () {
    echo "</body>"
    echo "</html>"
}

if [[ ${1:-} == "--help" ]]; then
    usage
    exit 0
fi

if [[ $# -lt 2 ]]; then
    fail "missing REPORT_TITLE or SECTION"
fi

report_title=$1
shift

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
        all)
            report_all
            ;;
        *)
            fail "unknown section: $section"
            ;;
    esac
done

report_footer
EOF

chmod +x lab-report.sh
```

---

# Exercise 3

## Run tests

```bash
./lab-report.sh --help
printf 'exit=%s
' "$?"

./lab-report.sh
printf 'exit=%s
' "$?"

./lab-report.sh "Title Only"
printf 'exit=%s
' "$?"

./lab-report.sh "My Report" system > report.html
printf 'exit=%s
' "$?"
grep -E '<h1>|<h2>' report.html

./lab-report.sh "My Report" all > report.html
printf 'exit=%s
' "$?"
grep -E '<h1>|<h2>' report.html

./lab-report.sh "My Report" bad > report.html
printf 'exit=%s
' "$?"
```

## Explain-back

Answer:

1. Which tests succeeded? --help, the successful system report, and the successful all port. 
2. Which tests failed? No arguments, title-only, and unknown section "bad". 
3. Did the failure cases exit nonzero? Yes. 
4. Did error messages go to the terminal even when stdout was redirected? Yes. 
5. Why is that useful? Error messages remain visible to the user even when outputs are redirected to a file. 
