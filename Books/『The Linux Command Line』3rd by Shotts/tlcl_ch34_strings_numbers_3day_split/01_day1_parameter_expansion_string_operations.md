# TLCL Chapter 34 — Strings and Numbers


Book: William Shotts, *The Linux Command Line*, Chapter 34, **“Strings and Numbers.”**

This chapter should be a **three-day work** because it contains many small Bash features that are easy to type mechanically and forget.

The goal is not to memorize every form of parameter expansion.

The goal is this:

```text
When a script receives text or a number, I can explain:
1. what value exists,
2. how Bash expands it,
3. whether it is a string or an integer,
4. what transformation I am applying,
5. how I verified the result.
```

Feynman rule:

```text
Do not say “Bash magic.”
Explain it as if teaching a younger student:
“This expression takes this variable, applies this rule, and produces this text.”
```

Disciplined thinking rule:

```text
Before typing:
- What value do I start with?
- What exact output do I expect?
- What happens if the variable is empty?
- What happens if the variable is unset?
- What happens if the number has strange input?

After running:
- Did Bash produce what I predicted?
- If not, which assumption was wrong?
```

---

## 3-day split

| Day | Read before exercises | Main gain |
|---|---|---|
| Day 1 | `Parameter Expansion`, `Basic Parameters`, `Expansions to Manage Empty Variables`, `Expansions That Return Variable Names`, `String Operations`, `Case Conversion` | Treat strings as data that Bash can inspect and transform |
| Day 2 | `Arithmetic Evaluation and Expansion`, `Number Bases`, `Unary Operators`, `Simple Arithmetic`, `Assignment`, `Bit Operations`, `Logic`, `The Comma Operator` | Treat numbers carefully; understand Bash integer arithmetic and operator behavior |
| Day 3 | `bc—An Arbitrary Precision Calculator Language`, `Using bc`, `An Example Script`, `Summing Up`, optional `Extra Credit` | Know when Bash arithmetic is not enough and build a small script using strings and numbers |

---

# Setup for all three days

Create a safe working directory:

```bash
mkdir -p ~/tlcl-ch34-strings-numbers
cd ~/tlcl-ch34-strings-numbers
```

Create a scratch script file when needed:

```bash
: > scratch.sh
```

Safety rule:

```text
Do all experiments in ~/tlcl-ch34-strings-numbers.
Do not run these examples in important project directories.
```

---

# Day 1 — Parameter Expansion and String Operations

## Read before doing exercises

Read these Chapter 34 sections first:

```text
Parameter Expansion
Basic Parameters
Expansions to Manage Empty Variables
Expansions That Return Variable Names
String Operations
Case Conversion
```

## What he should gain from these sections

He should gain this skill:

```text
I can use Bash itself to inspect, default, slice, trim, replace, and change the case of strings stored in variables.
```

This matters because many scripts spend most of their time handling strings:

```text
filenames
hostnames
paths
user input
command arguments
configuration values
```

Feynman analogy:

```text
A variable is like a labeled box.
Parameter expansion is how Bash opens the box and optionally cuts, changes, or checks what is inside.
```

---

## After reading: concept questions

Answer these before touching the keyboard:

1. What is parameter expansion?
2. What is the difference between `$name` and `${name}`?
3. Why are braces useful when variable names touch other characters?
4. What is the difference between an unset variable and an empty variable?
5. What problem do default-value expansions solve?
6. Why can string operations be useful before calling tools like `sed`, `cut`, or `tr`?
7. What is one risk of manipulating strings without quoting variables?
8. What does it mean to remove the shortest matching prefix or suffix?
9. What does case conversion do?
10. Which forms from this section look useful immediately, and which are only recognition-level for now?

Do not proceed until he can answer at least 8 of 10.

---

## Day 1 Exercise 1 — Basic parameter expansion

### Read again before the exercise

Reread:

```text
Basic Parameters
```

### Skill being gained

He is learning that Bash expands variables before running the command.

### Do not type yet: predict

For each command below, predict the output first.

```text
What variable exists?
What text will Bash substitute?
Will the shell see one word or several words?
```

### Run

```bash
cd ~/tlcl-ch34-strings-numbers

name="app01"
echo "$name"
echo "server=$name"
echo "server=${name}"
echo "${name}_backup"
```

Now test the brace problem:

```bash
name="app"
echo "$name01"
echo "${name}01"
```

### Explain-back

Answer:

1. Why did `${name}01` work better than `$name01`?
2. What variable did Bash try to expand in `$name01`?
3. Why should a disciplined script writer use braces when the variable touches more text?

---

## Day 1 Exercise 2 — Empty and unset variables

### Read again before the exercise

Reread:

```text
Expansions to Manage Empty Variables
```

### Skill being gained

He is learning to handle missing input deliberately instead of letting scripts behave accidentally.

### Do not type yet: predict

Define these terms in writing:

```text
unset variable:
empty variable:
default value:
required value:
```

### Run

```bash
unset color
size=""
shape="circle"

printf 'color=<%s>\n' "${color:-blue}"
printf 'size=<%s>\n' "${size:-medium}"
printf 'shape=<%s>\n' "${shape:-square}"
```

Now compare with a form that assigns a default:

```bash
unset envname
printf 'before envname=<%s>\n' "$envname"
printf 'defaulted envname=<%s>\n' "${envname:=dev}"
printf 'after envname=<%s>\n' "$envname"
```

### Explain-back

Answer:

1. What did `:-` do?
2. What did `:=` do?
3. Which one changed the variable?
4. Why is this useful for script options and defaults?
5. Why can defaulting silently be dangerous if the user really needed to provide a value?

---

## Day 1 Exercise 3 — Required values

### Skill being gained

He is learning how a script can fail early when required data is missing.

### Do not type yet: predict

Predict what happens if `target` is unset:

```bash
echo "${target:?target is required}"
```

Will the script continue or stop?

### Create a tiny script

```bash
cat > required-test.sh <<'EOF'
#!/usr/bin/env bash

printf 'Target is: %s\n' "${target:?target is required}"
printf 'This line runs only if target exists.\n'
EOF

bash required-test.sh
```

Now run with `target` set:

```bash
target="app01" bash required-test.sh
```

### Explain-back

Answer:

1. What problem does `${variable:?message}` solve?
2. Why is failing early often better than continuing with an empty value?
3. When might this be too harsh for a beginner script?

---

## Day 1 Exercise 4 — String length, slicing, and trimming

### Read again before the exercise

Reread:

```text
String Operations
```

### Skill being gained

He is learning to answer questions about a string without immediately reaching for external commands.

### Do not type yet: predict

Given:

```bash
path="/var/log/nginx/access.log"
```

Predict:

```text
length of path: roughly how many characters?
first 4 characters:
remove shortest prefix ending in /:
remove longest prefix ending in /:
remove shortest suffix starting with .:
```

### Run

```bash
path="/var/log/nginx/access.log"

printf 'path=<%s>\n' "$path"
printf 'length=<%s>\n' "${#path}"
printf 'first four=<%s>\n' "${path:0:4}"
printf 'remove shortest prefix ending slash=<%s>\n' "${path#*/}"
printf 'remove longest prefix ending slash=<%s>\n' "${path##*/}"
printf 'remove shortest suffix beginning dot=<%s>\n' "${path%.*}"
printf 'remove longest suffix beginning dot=<%s>\n' "${path%%.*}"
```

### Explain-back

Teach this to a younger student:

```text
# removes from the front.
% removes from the back.
Single symbol means shortest match.
Double symbol means longest match.
```

Then answer:

1. Which expression gave the filename only?
2. Which expression removed the extension?
3. Why does `##*/` often appear in scripts that process paths?
4. Why must the result still be quoted when used later?

---

## Day 1 Exercise 5 — Replacement and case conversion

### Read again before the exercise

Reread:

```text
String Operations
Case Conversion
```

### Skill being gained

He is learning to transform variable contents in controlled ways.

### Do not type yet: predict

Given:

```bash
host="App01-Prod"
```

Predict:

```text
lowercase:
uppercase:
replace first hyphen with underscore:
replace every p with X:
```

### Run

```bash
host="App01-Prod"

printf 'original=<%s>\n' "$host"
printf 'lower=<%s>\n' "${host,,}"
printf 'upper=<%s>\n' "${host^^}"
printf 'first hyphen replaced=<%s>\n' "${host/-/_}"
printf 'all p replaced=<%s>\n' "${host//p/X}"
```

### Explain-back

Answer:

1. Why might lowercasing user input help a script?
2. What is the difference between `${host/-/_}` and `${host//-/_}`?
3. Why is this sometimes simpler than using `sed` or `tr`?
4. When would `sed`, `awk`, or another tool still be better?

---

## Day 1 final mini-lab — Normalize a hostname

### Read after exercise

After doing the exercise, reread:

```text
String Operations
Case Conversion
```

This time read with this question:

```text
Which expansions are useful enough to remember, and which only need recognition?
```

### Task

Create `normalize-host.sh`:

```bash
cat > normalize-host.sh <<'EOF'
#!/usr/bin/env bash

raw=${1:?usage: normalize-host.sh HOSTNAME}

lower=${raw,,}
clean=${lower// /-}
short=${clean%%.*}

printf 'raw=<%s>\n' "$raw"
printf 'lower=<%s>\n' "$lower"
printf 'clean=<%s>\n' "$clean"
printf 'short=<%s>\n' "$short"
EOF
```

Test:

```bash
bash normalize-host.sh "App01 Prod.example.com"
bash normalize-host.sh "DB01.EXAMPLE.COM"
bash normalize-host.sh
```

### Explain-back

Answer:

1. What does each variable store?
2. Which line lowercases the input?
3. Which line replaces spaces?
4. Which line removes the domain?
5. Why does the script stop when no hostname is given?
6. What input might still break or surprise this script?

---

## Day 1 finish standard

He is done with Day 1 only if he can explain:

```text
${var}
${var:-default}
${var:=default}
${var:?message}
${#var}
${var:offset:length}
${var#pattern}
${var##pattern}
${var%pattern}
${var%%pattern}
${var/pattern/replacement}
${var//pattern/replacement}
${var,,}
${var^^}
```

He does not need instant memorization of every form, but he must know what problem each form solves.

---
