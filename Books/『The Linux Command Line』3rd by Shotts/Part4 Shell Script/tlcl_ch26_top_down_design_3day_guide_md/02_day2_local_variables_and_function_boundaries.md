# Shotts Chapter 26: Top-Down Design

This guide is for Chapter 26, **Top-Down Design**, from William Shotts's *The Linux Command Line*.

The goal is not merely to type functions.

The goal is to learn this habit:

```text
Start with the big job.
Break it into named smaller jobs.
Make each piece work.
Test after every small change.
Keep the script runnable.
```

Feynman analogy:

```text
A messy script is like a long paragraph with no headings.
Functions are like section headings.
They let the reader see the shape of the work before reading every detail.
```

Main project for all three days:

```text
lab-report.sh
```

It will grow from a simple report script into a script organized with functions.

Create the working directory once:

```bash
mkdir -p ~/tlcl-ch26-top-down
cd ~/tlcl-ch26-top-down
```

Disciplined thinking rule:

```text
Before typing a function, say what job it owns.
Before running a script, predict what section should print.
After running, inspect the output and explain what changed.
```

---
# Day 2: Local Variables and Function Boundaries

## Read before exercises

Read the Chapter 26 section:

```text
Local Variables
```

## What he should gain from this reading

He should gain this idea:

```text
Functions should not accidentally leak temporary variable names into the rest of the script.
```

He is learning **boundaries**.

A disciplined function should have a clear job and should avoid surprising the rest of the script.

---

# Before reading: Feynman preview

Imagine each function is a worker at a desk.

A worker may use scratch paper.

That scratch paper should not become a rule for the whole office.

```text
global variable = posted on the office wall
local variable = scratch paper on one worker's desk
```

If a function only needs a variable temporarily, make it local.

---

# After reading: concept questions

Answer without looking back:

1. What is a local variable?
2. Why can global variables cause confusion?
3. How does `local` help inside a function?
4. Can `local` be used outside a function?
5. Why is it useful for functions to have boundaries?
6. How does this help debugging?

Do not do the exercises until these are answered.

---

# Exercise 1: Observe variable leakage

## Skill being gained

He is learning why local variables matter.

## Predict before typing

Before running, predict the output:

```text
What will animal be before the function?
What will animal be inside the function?
What will animal be after the function?
```

## Create a test script

```bash
cd ~/tlcl-ch26-top-down

cat > variable-leak-test.sh <<'EOF'
#!/usr/bin/env bash

animal="cat"

change_animal () {
    animal="dog"
    echo "Inside function: animal=$animal"
}

echo "Before function: animal=$animal"
change_animal
echo "After function: animal=$animal"
EOF

bash variable-leak-test.sh
```

## Explain-back

Answer:

1. Did the function change the outside variable?
2. Why could that be dangerous in a larger script?
3. What kind of bug could this create?

---

# Exercise 2: Use `local`

## Skill being gained

He is learning to keep temporary data inside a function.

## Predict before typing

Before running, predict:

```text
Will animal outside the function remain cat?
Why?
```

## Create the safer version

```bash
cat > local-variable-test.sh <<'EOF'
#!/usr/bin/env bash

animal="cat"

change_animal () {
    local animal="dog"
    echo "Inside function: animal=$animal"
}

echo "Before function: animal=$animal"
change_animal
echo "After function: animal=$animal"
EOF

bash local-variable-test.sh
```

## Explain-back

Explain in plain English:

```text
The function had its own variable named animal.
The outside script had another variable named animal.
The local one did not overwrite the outside one.
```

---

# Exercise 3: Improve the report script with local variables

## Skill being gained

He is learning to use local variables for readability inside functions.

## Read before exercise

Reread the “Local Variables” section briefly.

Ask:

```text
What variable names in my function are temporary?
What names should not leak into other parts of the script?
```

## Add a function using local variables

Open `lab-report.sh` and improve `report_disk_space`.

Use this function:

```bash
report_disk_space () {
    local title="Disk Space"
    echo "<h2>$title</h2>"
    echo "<pre>"
    df -h
    echo "</pre>"
}
```

Run:

```bash
bash lab-report.sh > report.html
grep -A10 "Disk Space" report.html
```

## Explain-back

Answer:

1. Why is `title` local?
2. What would happen if another function also used a variable named `title`?
3. Why does local make this function safer?
4. Does `df -h` write to stdout, stderr, or a file?
5. Where does that output go when the whole script is redirected to `report.html`?

---

# Exercise 4: Make functions explainable

## Skill being gained

He is learning to make functions small and named clearly.

For each function in `lab-report.sh`, write:

```text
Function:
Purpose:
Temporary variables:
Commands used:
Expected output:
Possible failure:
```

Example:

```text
Function: report_disk_space
Purpose: print HTML section for disk-space usage
Temporary variables: title
Commands used: echo, df
Expected output: h2 heading and preformatted df output
Possible failure: df command unavailable or unusual output
```

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
A function should have a clear boundary.
Temporary variables inside a function should usually be local.
Local variables reduce accidental interference between functions.
I can explain where each variable is used and why.
```

He must also be able to explain the difference between the two outputs:

```bash
bash variable-leak-test.sh
bash local-variable-test.sh
```
