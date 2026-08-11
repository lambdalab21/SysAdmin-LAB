# TLCL Chapter 33: Flow Control — Looping with `for`

Use this chapter to learn how to repeat work over a list.

The point is not to memorize syntax. The point is to think clearly about:

```text
What is my list?
What is one item?
What happens once per item?
What could go wrong if the list is not what I think it is?
```

Feynman analogy:

```text
A for loop is like a teacher handing out one worksheet to each student.
The class list is the input list.
The current student is the loop variable.
The repeated instruction is the loop body.
The teacher should know who is on the list before starting.
```

Working directory:

```bash
mkdir -p ~/tlcl-ch33-for
cd ~/tlcl-ch33-for
```

---
# Day 2: `for` with Files, Globs, Arguments, and Generated Lists

## Read before exercises

Reread the full section:

```text
for: Traditional Shell Form
```

This time, focus on where the list can come from:

```text
literal words
shell expansions
filename globs
script arguments
command substitution
```

## What he should gain

He should gain this habit:

```text
Before using a for loop, identify how Bash builds the list.
```

This is the day that prevents mindless and dangerous shell scripting.

---

# After reading: concept questions

Answer before typing:

1. What are three possible sources of the list in a `for` loop?
2. When does glob expansion happen: before the loop starts or inside each pass?
3. Why can filenames with spaces be dangerous if variables are not quoted?
4. What does `"$@"` mean in a script?
5. Why is `"$@"` usually safer than `$*` or unquoted `$@`?
6. Why should dangerous loops start with `printf` or `echo`?
7. What can go wrong with `for file in $(ls)`?
8. What question should you ask before looping over command output?

Do not continue until these are answered.

---

# Exercise 1: Globs are expanded before the loop body runs

## Read before this exercise

Reread the part of the chapter where lists are supplied after `in`. Then connect it to Shotts Chapter 7 on shell expansion.

## Skill being gained

He is learning that the shell prepares the list before the `for` loop processes it.

## Setup

```bash
cd ~/tlcl-ch33-for
mkdir -p files
cd files
: > alpha.txt
: > beta.txt
: > gamma.log
: > "two words.txt"
cd ..
```

## Do not type yet

Predict what this will print:

```bash
for file in files/*.txt; do
    printf '<%s>\n' "$file"
done
```

Answer:

```text
What does files/*.txt expand to?
How many loop passes will there be?
Will "two words.txt" stay one item?
Why?
```

## Run

```bash
for file in files/*.txt; do
    printf '<%s>\n' "$file"
done
```

## Read after this exercise

Review Chapter 7 if necessary: pathname expansion happens before the command receives its arguments. Then explain how that applies to `for file in files/*.txt`.

## Explain-back

Explain:

```text
The shell first expands files/*.txt into ...
Then the for loop assigns each filename to file, one at a time.
Inside the loop, "$file" protects filenames with spaces.
```

---

# Exercise 2: Preview before action

## Read before this exercise

Reread the `for` traditional form and remind yourself that the loop body can contain dangerous commands. The danger is not the loop itself; the danger is repeating the wrong action many times.

## Skill being gained

He is learning to preview batch actions before performing them.

## Do not type yet

Suppose you want to rename `.log` files. First answer:

```text
What files might match files/*.log?
What would be dangerous if the pattern matched more than expected?
What preview command should I run first?
```

## Preview only

```bash
for file in files/*.log; do
    printf 'Would process: <%s>\n' "$file"
done
```

## Optional safe action

This creates copies, not destructive moves:

```bash
mkdir -p files/archive-preview
for file in files/*.log; do
    cp -- "$file" files/archive-preview/
done

find files/archive-preview -type f -print
```

## Read after this exercise

Reread the loop syntax and identify exactly which part is repeated. Then review `--` as a safety separator for filenames beginning with `-`.

## Explain-back

Answer:

1. Why is `cp -- "$file" files/archive-preview/` safer than `cp $file files/archive-preview/`?
2. Why did we preview before copying?
3. Why did we copy instead of moving or removing?
4. What would change if no `.log` files existed?

---

# Exercise 3: Loop over script arguments with `"$@"`

## Read before this exercise

Reread Chapter 32 on positional parameters, especially `"$@"`. Then return to Chapter 33 and connect arguments to `for` loops.

## Skill being gained

He is learning to process command-line arguments safely.

## Create script

```bash
cat > arg-preview.sh <<'EOF'
#!/usr/bin/env bash

# arg-preview.sh - safely preview each argument

for item in "$@"; do
    printf 'Argument: <%s>\n' "$item"
done
EOF
```

## Do not type yet

Predict output for:

```bash
bash arg-preview.sh alpha "two words" gamma
```

How many arguments should there be?

## Run

```bash
bash arg-preview.sh alpha "two words" gamma
```

Then compare with this intentionally unsafe script:

```bash
cat > arg-preview-unsafe.sh <<'EOF'
#!/usr/bin/env bash

for item in $@; do
    printf 'Argument: <%s>\n' "$item"
done
EOF

bash arg-preview-unsafe.sh alpha "two words" gamma
```

## Read after this exercise

Review Chapter 32 again if the difference between `"$@"` and unquoted `$@` is not obvious.

## Explain-back

Answer:

1. Why did `"two words"` stay together in the safe version?
2. Why did the unsafe version split it?
3. Why does this matter for filenames?
4. When writing real scripts, what should the default habit be?

---

# Exercise 4: Avoid `for file in $(ls)`

## Read before this exercise

Reread Chapter 7 on command substitution and Chapter 33 on list construction. Ask what happens when command output is split into words.

## Skill being gained

He is learning not to turn filenames into unsafe word lists.

## Do not type yet

Predict what happens with a filename containing spaces:

```bash
for file in $(ls files/*.txt); do
    printf '<%s>\n' "$file"
done
```

## Run

```bash
for file in $(ls files/*.txt); do
    printf '<%s>\n' "$file"
done
```

Now run the safer form:

```bash
for file in files/*.txt; do
    printf '<%s>\n' "$file"
done
```

## Read after this exercise

Review the difference between:

```text
looping over a glob
looping over command substitution output
```

## Explain-back

Explain why this is usually wrong:

```bash
for file in $(ls)
```

Use these words:

```text
word splitting
filenames with spaces
shell expansion
safe glob
```

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
I know where the loop list comes from.
I know globs are expanded by the shell before the loop runs.
I quote "$file" inside the loop.
I use "$@" for script arguments.
I preview loops before doing batch actions.
I avoid for file in $(ls).
```
