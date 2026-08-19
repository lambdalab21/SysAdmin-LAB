# TLCL Chapter 35: Arrays

This guide is for Chapter 35, **Arrays**, from William Shotts's *The Linux Command Line*.

Use this chapter as a practical introduction to Bash arrays, not as a reason to turn shell scripts into Python programs.

Feynman rule for this chapter:

```text
An array is a labeled row of boxes.
The array name identifies the row.
The index identifies one box.
The expansion decides whether Bash gives you one box, every box, or the box labels.
```

Core discipline:

```text
Before expanding an array, say exactly what you expect Bash to produce.
Then use printf to inspect it.
Only after that use the values in a loop or command.
```

Working directory for all three days:

```bash
mkdir -p ~/tlcl-ch35-arrays
cd ~/tlcl-ch35-arrays
```

Safety reminder:

```text
Arrays often hold filenames or user-provided text.
Always test with spaces in names.
Always quote array expansions unless you have a specific reason not to.
```

---

# Day 3: Associative Arrays and Final Lab

## Read before exercises

Read Chapter 35 sections:

```text
Associative Arrays
Summing Up
```

## What he should gain

He should gain this idea:

```text
Indexed arrays use numbers as labels.
Associative arrays use names as labels.
```

This is useful for simple key/value data, but it can also make shell scripts too complicated if overused.

---

# Before reading: Feynman preview

Explain this before reading:

```text
An indexed array is like apartment numbers: 0, 1, 2.
An associative array is like labeled drawers: error, warning, info.
The label tells me what the value means.
```

Do not type yet. First answer:

1. When is a named key clearer than a number?
2. Why might `status_color[error]=red` be clearer than `colors[0]=red`?
3. Why might associative arrays be unnecessary for very small scripts?

---

# After reading: concept questions

Answer without looking back:

1. What is an associative array?
2. How is it different from an indexed array?
3. How do you declare an associative array in Bash?
4. How do you assign a value to a key?
5. How do you access a value by key?
6. How do you list all keys?
7. Why is this Bash-specific?
8. When should you avoid making a shell script too complex with arrays?

---

# Exercise 1: Create a simple associative array

## Skill being gained

He is learning key/value lookup in Bash.

## Predict before typing

Answer:

```text
What are the keys?
What are the values?
What should ${status_color[error]} produce?
```

## Commands

```bash
cd ~/tlcl-ch35-arrays

cat > day3-associative-basic.sh <<'EOF'
#!/usr/bin/env bash

declare -A status_color

status_color[error]=red
status_color[warning]=yellow
status_color[ok]=green

printf 'error   -> %s\n' "${status_color[error]}"
printf 'warning -> %s\n' "${status_color[warning]}"
printf 'ok      -> %s\n' "${status_color[ok]}"
EOF

bash day3-associative-basic.sh
```

## Explain-back

Answer:

1. Why did we use `declare -A`?
2. What is the key in `status_color[error]`?
3. What is the value?
4. Why is this more readable than remembering that index 0 means error?

---

# Exercise 2: Loop over keys

## Skill being gained

He is learning to inspect all key/value pairs.

## Predict before typing

Answer:

```text
What should ${!status_color[@]} produce?
Will the keys necessarily appear in the order I assigned them?
Should I rely on associative-array order?
```

## Commands

```bash
cat > day3-associative-loop.sh <<'EOF'
#!/usr/bin/env bash

declare -A status_color=(
    [error]=red
    [warning]=yellow
    [ok]=green
)

printf 'Keys:\n'
printf '  <%s>\n' "${!status_color[@]}"

printf 'Key/value pairs:\n'
for key in "${!status_color[@]}"; do
    printf '  %s -> %s\n' "$key" "${status_color[$key]}"
done
EOF

bash day3-associative-loop.sh
```

## Explain-back

Answer:

1. What did `${!status_color[@]}` expand to?
2. Why did the loop use `"${!status_color[@]}"`?
3. Why did the value use `${status_color[$key]}`?
4. Should a script depend on the printed order of keys?

---

# Exercise 3: Final lab — script report using arrays

## Skill being gained

He is learning to use arrays only where they make the script clearer.

## Read before exercise

Reread:

```text
Array Operations
Associative Arrays
Summing Up
```

Before typing, answer:

```text
What list will the indexed array hold?
What lookup will the associative array hold?
Why are arrays useful here?
Would plain variables be simpler?
```

## Create the final script

```bash
cat > array-report.sh <<'EOF'
#!/usr/bin/env bash

# array-report.sh - small report using indexed and associative arrays

servers=(app01 db01 playground01)

declare -A role=(
    [app01]=web
    [db01]=database
    [playground01]=test
)

report_header () {
    echo "Server Report"
    echo "============="
}

report_servers () {
    local server

    printf 'Number of servers: %s\n' "${#servers[@]}"
    printf '\nServers:\n'

    for server in "${servers[@]}"; do
        printf '  %-12s role=%s\n' "$server" "${role[$server]}"
    done
}

report_debug_view () {
    printf '\nDebug view:\n'
    printf '  indexes: <%s>\n' "${!servers[@]}"
    printf '  keys:    <%s>\n' "${!role[@]}"
}

report_header
report_servers
report_debug_view
EOF

bash -n array-report.sh
bash array-report.sh
```

## Explain-back

Answer:

1. What does the indexed array `servers` hold?
2. What does the associative array `role` hold?
3. Why does the loop use `"${servers[@]}"`?
4. Why does the role lookup use `${role[$server]}`?
5. What happens if `servers` contains a server with no role key?
6. How could you detect that problem?

Try this experiment:

```bash
cp array-report.sh array-report-missing-role.sh
```

Edit the copy and add `cache01` to the `servers` array but not to the `role` array. Then run it.

Answer:

```text
What did the missing role look like?
Should the script accept that silently?
What test could detect it?
```

---

# Exercise 4: Add validation for missing keys

## Skill being gained

He is learning that arrays do not remove the need for validation.

## Predict before typing

Answer:

```text
How can I test whether a role exists for a server?
What should the script print when a role is missing?
```

## Modify only the loop

Use this safer version of `report_servers`:

```bash
report_servers () {
    local server

    printf 'Number of servers: %s\n' "${#servers[@]}"
    printf '\nServers:\n'

    for server in "${servers[@]}"; do
        if [[ -v "role[$server]" ]]; then
            printf '  %-12s role=%s\n' "$server" "${role[$server]}"
        else
            printf '  %-12s role=%s\n' "$server" "UNKNOWN"
        fi
    done
}
```

Run:

```bash
bash -n array-report.sh
bash array-report.sh
```

## Explain-back

Answer:

1. What does `[[ -v "role[$server]" ]]` test?
2. Why is validation still needed even when using arrays?
3. What would be a better behavior in a real admin script: print `UNKNOWN`, warn, or exit? Why?

---

# Final concept questions

Answer in writing:

1. What problem do arrays solve in Bash scripts?
2. What is the difference between indexed arrays and associative arrays?
3. What does `"${array[@]}"` do?
4. What does `${#array[@]}` do?
5. What does `${!array[@]}` do?
6. Why can array indexes have gaps?
7. How do you append to an array?
8. How do you delete one element?
9. Why should array expansions usually be quoted?
10. When should you avoid using arrays in a shell script?

---

# Final Feynman explain-back

Explain Chapter 35 to a younger student:

```text
An array is a variable that can hold many values.
An indexed array uses numbers as labels.
An associative array uses names as labels.
The dangerous part is expansion: Bash can turn an array into separate words, one big word, or nothing.
A disciplined shell user previews expansions with printf and quotes values carefully.
```

Then explain this line by line:

```bash
for server in "${servers[@]}"; do
    printf '%s -> %s\n' "$server" "${role[$server]}"
done
```

---

# Day 3 finish standard

He is done with Chapter 35 only if he can say:

```text
I can create indexed and associative arrays.
I can access one element and all elements.
I can loop safely over array values.
I can count elements and inspect indexes or keys.
I can validate missing associative-array keys.
I know arrays are useful, but Bash should not become an overcomplicated programming language when another tool would be better.
```
