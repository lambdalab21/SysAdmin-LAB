# Day 3 — Arrays, Parameter Expansion, and Final Lab

## Read before exercises

Read or review the Effective Shell Ch. 10 sections on:

```text
Managing Multiple Values with Arrays
Storing Complex Data with Associative Arrays
Expanding Shell Parameters
Enhancing the common Command with Variables
Summary
```

Treat arrays and associative arrays as **recognition-level** for now unless he is ready.

TLCL covers arrays later. Do not over-drill them before he is comfortable with simple variables, loops, and quoting.

## What he should gain

He should gain this practical skill:

```text
I can recognize when a value is one string versus many values, and I know the safe form "${array[@]}" when I must handle multiple values.
```

He should also gain early awareness of parameter expansion:

```text
Bash has built-in ways to modify or supply variable values during expansion.
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
A normal variable holds one value.
An array holds a list of values.
But each value may still contain spaces, so quoting remains important.
```

The safe mental model:

```text
"${array[@]}" means: expand each array element as its own protected argument.
```

---

# After reading: concept questions

Answer without looking back:

1. What is the difference between a variable and an array?
2. What is an array index?
3. Why is `"${array[@]}"` safer than `${array[*]}` for many script tasks?
4. What is an associative array?
5. Why might associative arrays be too much for early Bash scripting?
6. What is parameter expansion?
7. What does it mean to provide a default value during expansion?
8. When should Bash not be used as a complex data language?

---

# Exercise 1: Array basics

## Skill being gained

He is learning the difference between one string and multiple values.

## Predict before typing

Predict how many lines each command prints:

```bash
servers=(app01 db01 "test server")
printf '<%s>\n' ${servers[@]}
printf '<%s>\n' "${servers[@]}"
```

## Run

```bash
cd ~/effective-shell-ch10

servers=(app01 db01 "test server")
printf '<%s>\n' ${servers[@]}
printf '<%s>\n' "${servers[@]}"
```

## Explain-back

Answer:

1. What happened to `test server` without quotes?
2. What happened with `"${servers[@]}"`?
3. Why does this matter when looping over filenames or hostnames?
4. What is the safe default form for array expansion?

---

# Exercise 2: Loop over an array safely

## Skill being gained

He is learning to process multiple values one at a time.

## Predict before typing

Predict the output:

```bash
for server in "${servers[@]}"; do
    printf 'checking <%s>\n' "$server"
done
```

## Run

```bash
for server in "${servers[@]}"; do
    printf 'checking <%s>\n' "$server"
done
```

## Explain-back

Answer:

1. What value does `server` have on each loop?
2. Why is `"${servers[@]}"` quoted?
3. Why is `"$server"` quoted?
4. Would this be useful for `app01`, `db01`, and `playground01` checks later?

---

# Exercise 3: Small parameter expansion preview

## Skill being gained

He is learning that Bash can supply defaults during expansion.

## Predict before typing

Predict output:

```bash
unset report_name
printf '<%s>\n' "${report_name:-lab-report}"
report_name="disk-report"
printf '<%s>\n' "${report_name:-lab-report}"
```

## Run

```bash
unset report_name
printf '<%s>\n' "${report_name:-lab-report}"
report_name="disk-report"
printf '<%s>\n' "${report_name:-lab-report}"
```

## Explain-back

Answer:

1. What happened when `report_name` was unset?
2. What happened after it was set?
3. Did this permanently assign `report_name` the first time?
4. Why might defaults be useful in scripts?

Do not go deep yet. This is a preview.

---

# Exercise 4: Final lab — improve a script using variables

## Skill being gained

He is learning to use variables only where they clarify the script.

## Create

```bash
cat > server-summary.sh <<'EOF'
#!/usr/bin/env bash

report_title="Server Summary"
today=$(date +%F)
servers=(app01 db01 playground01)

printf '# %s\n' "$report_title"
printf 'Date: %s\n\n' "$today"

for server in "${servers[@]}"; do
    printf 'Server: %s\n' "$server"
    printf 'Status: TODO check later\n\n'
done
EOF

bash server-summary.sh
```

## Improve one small thing

Add a default title using parameter expansion:

```bash
report_title="${REPORT_TITLE:-Server Summary}"
```

Run normally:

```bash
bash server-summary.sh
```

Run with an environment variable:

```bash
REPORT_TITLE="Lab Machine Summary" bash server-summary.sh
```

## Explain-back

Answer:

1. Where did `REPORT_TITLE` come from in the second run?
2. Why did the script use a default title?
3. Why were array expansions quoted?
4. Which variables were simple strings?
5. Which variable was an array?
6. Could this script become part of the `lab-report.sh` project from TLCL Ch. 25–26?

---

# Day 3 finish standard

He is done only if he can explain:

```text
A scalar variable stores one value.
An array stores multiple values.
"${array[@]}" safely expands each element as a separate argument.
Parameter expansion can provide defaults.
Bash can handle simple lists, but complex data should usually move to Python or another language.
```
