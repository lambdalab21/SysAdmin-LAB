# TLCL Chapter 32: Positional Parameters

# Day 1: Accessing the Command Line

## Read before exercises

Read Chapter 32 from the beginning through:

```text
Accessing the Command Line
```

# After reading: concept questions

Answer without looking back:

1. What does `$0` usually contain? The script name. 
2. What does `$1` contain? The first command-line argument. 
3. What does `$2` contain? The second command-line argument. 
4. What does `$#` tell you? How many arguments were passed. 
5. Why are these called positional parameters? Their value depends on their position on the command line. 
6. Why should a script check the number of arguments before using them? To avoid acting on missing or incorrect input. 
7. What might go wrong if `$1` is empty but the script assumes it is a filename? Commands treat an empty value as no filenames or a wrong path. 
8. Why should arguments usually be quoted when used? To prevent word-splitting and globbing. 

---

# Exercise 1: See what the script receives

## Create the script

```bash
cd ~/tlcl-ch32-positional-parameters

cat > show-args.sh <<'EOF'
#!/usr/bin/env bash

echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "Third argument: $3"
echo "Number of arguments: $#"
EOF

chmod +x show-args.sh
./show-args.sh alpha beta gamma
```

## Test different cases

Run these one at a time:

```bash
./show-args.sh
./show-args.sh alpha
./show-args.sh alpha beta
./show-args.sh "alpha beta" gamma
```

## Explain-back

Answer:

1. What changed when no arguments were given? $1-$3 empty; $0 is 0. 
2. What changed when one argument contained a space? The quoted phrase stays as one argument. 
3. Why did quotes around `"alpha beta"` matter? Quotes kept the space inside a single argument. 
4. Did `$#` count words or arguments? Arguments. 

---

# Exercise 2: Add argument checking

## Do not type yet

Design the rule in English:

```text
This script requires exactly two arguments:
1. source
2. destination

If the user does not give exactly two arguments, print usage and fail.
```

## Create the script

```bash
cat > require-two.sh <<'EOF'
#!/usr/bin/env bash

if [[ $# -ne 2 ]]; then
    echo "Usage: $0 SOURCE DESTINATION" >&2
    exit 1
fi

source=$1
destination=$2

echo "Source: $source"
echo "Destination: $destination"
EOF

chmod +x require-two.sh
```

## Test both branches

```bash
./require-two.sh
./require-two.sh one
./require-two.sh one two
./require-two.sh one two three
```

## Explain-back

Answer:

1. What does `[[ $# -ne 2 ]]` mean in English? Number of arguments is not equal to 2. 
2. Which test cases should fail? Zero, one, or three arguments. 
3. Which test case should pass? Exactly two arguments. 
4. Why does the usage message go to `stderr` with `>&2`? Keeps usage separate from normal output. 
5. Why does the script use `exit 1`? Signals failure to the caller. 

---

# Exercise 3: Upgrade the report script to accept a title

## Create the script

```bash
cat > lab-report.sh <<'EOF'
#!/usr/bin/env bash

if [[ $# -ne 1 ]]; then
    echo "Usage: $0 REPORT_TITLE" >&2
    exit 1
fi

report_title=$1

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

report_footer () {
    echo "</body>"
    echo "</html>"
}

report_header
report_system_info
report_footer
EOF

chmod +x lab-report.sh
```

Run:

```bash
./lab-report.sh "App01 Lab Report" > report.html
grep -E '<title>|<h1>' report.html
```

## Explain-back

Answer:

1. Why must the title be quoted on the command line? So spaces stay inside one argument. 
2. What would happen with `./lab-report.sh App01 Lab Report`? $# becomes 3; the script rejects it. 
3. Why is `report_title=$1` near the top? Capture the validated argument early for later use. 
4. Why is `$report_title` quoted in ordinary shell code but inside HTML text it appears as part of a string? Inside the HTML string, it's just text being written. Quoting still protects expansion when building the string in the shell. 