# Day 1 — Variable Basics, Export, Quoting, and Braces

## Read before exercises

Read the beginning of Effective Shell Ch. 10 through the sections on:

```text
Shell Variables
Exporting Shell Variables as Environment Variables
Variable Syntax
Quoting Variables and Values
Using Braces to Reference Variables Explicitly
```

## What he should gain

He should gain this practical skill:

```text
I can store a value, expand it safely, decide whether to export it, and prevent the shell from damaging it through word splitting or globbing.
```

This is mostly a refresh of TLCL expansion/quoting plus TLCL scripting variables.

---

# Before reading: Feynman preview

Explain this before reading:

```text
A variable is a name attached to a value.
But using the variable means asking the shell to replace the name with its value.
After replacement, the shell may still do word splitting and globbing unless I quote it.
```

The danger is not only the variable.

The danger is what the shell does **after** expanding the variable.

---

# After reading: concept questions

Answer without looking back:

1. What is the difference between assigning a variable and expanding a variable?
2. Why is there no space around `=` in `name=value`?
3. What does `$name` mean?
4. Why can `${name}` be clearer than `$name`?
5. What is the difference between a shell variable and an environment variable?
6. What does `export` do?
7. Why should variable expansions usually be quoted?
8. What can go wrong with an unquoted variable containing spaces?
9. What can go wrong with an unquoted variable containing `*`?
10. Why is `printf` often better than `echo` for inspection?

Do not do the exercises until these are answered.

---

# Exercise 1: Assignment and expansion

## Skill being gained

He is learning that assignment and expansion are different operations.

## Predict before typing

Predict each output:

```bash
name="app01"
printf '<%s>\n' "$name"
printf '<%s>\n' '$name'
printf '<%s>\n' "${name}_backup"
```

## Run

```bash
mkdir -p ~/effective-shell-ch10
cd ~/effective-shell-ch10

name="app01"
printf '<%s>\n' "$name"
printf '<%s>\n' '$name'
printf '<%s>\n' "${name}_backup"
```

## Explain-back

Answer:

1. Which command expanded the variable?
2. Which command printed `$name` literally?
3. Why were braces useful in `${name}_backup`?
4. What would `$name_backup` mean instead?

---

# Exercise 2: Quoting and word splitting

## Skill being gained

He is learning why `"$var"` is the safe default.

## Predict before typing

Predict how many arguments each command will print.

```bash
filename="my report.txt"
printf '<%s>\n' $filename
printf '<%s>\n' "$filename"
```

## Run

```bash
filename="my report.txt"
printf '<%s>\n' $filename
printf '<%s>\n' "$filename"
```

## Explain-back

Answer:

1. Why did the unquoted variable become two arguments?
2. Why did the quoted variable remain one argument?
3. Why does this matter for filenames?
4. Which version should be the default in scripts?

---

# Exercise 3: Quoting and accidental globbing

## Skill being gained

He is learning that unquoted variable output can be globbed.

## Setup

```bash
touch a.txt b.txt c.log
pattern="*.txt"
```

## Predict before typing

Predict the difference:

```bash
printf '<%s>\n' $pattern
printf '<%s>\n' "$pattern"
```

## Run

```bash
printf '<%s>\n' $pattern
printf '<%s>\n' "$pattern"
```

## Explain-back

Answer:

1. Did the unquoted value stay as `*.txt`?
2. Who expanded the glob?
3. Why would this be dangerous with `rm $pattern`?
4. Why is `printf '<%s>\n'` a useful preview habit?

---

# Exercise 4: Export and child processes

## Skill being gained

He is learning that child processes do not automatically inherit ordinary shell variables.

## Predict before typing

Predict which child shell sees the variable:

```bash
lesson="variables"
bash -c 'printf "lesson=<%s>\n" "$lesson"'
export lesson
bash -c 'printf "lesson=<%s>\n" "$lesson"'
```

## Run

```bash
lesson="variables"
bash -c 'printf "lesson=<%s>\n" "$lesson"'
export lesson
bash -c 'printf "lesson=<%s>\n" "$lesson"'
```

## Explain-back

Answer:

1. What did the child shell see before `export`?
2. What did it see after `export`?
3. Why does `PATH` need to be an environment variable?
4. Why should you avoid exporting variables unnecessarily?

---

# Day 1 finish standard

He is done only if he can explain:

```text
name=value stores a value.
$name or ${name} expands the value.
"$name" usually protects the value.
export passes the variable to child processes.
Unquoted expansion can cause word splitting and globbing.
```
