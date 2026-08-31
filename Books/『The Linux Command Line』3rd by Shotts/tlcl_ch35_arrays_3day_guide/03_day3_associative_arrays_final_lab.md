# TLCL Chapter 35: Arrays

This guide is for Chapter 35, **Arrays**, from William Shotts's *The Linux Command Line*.

Use this chapter as a practical introduction to Bash arrays, not as a reason to turn shell scripts into Python programs.

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
# After reading: concept questions

Answer without looking back:

<<<<<<< HEAD
<<<<<<< HEAD
1. What is an associative array? An array that uses named keys instead of numbers to index values. 
2. How is it different from an indexed array? Indexed arrays use sequential integers as labels; associative arrays use arbitrary strings as labels. 
3. How do you declare an associative array in Bash? `declare -A array_name`
4. How do you assign a value to a key? `array_name[key]=value`
5. How do you access a value by key? `${array_name[key]}`
6. How do you list all keys? `${!array_name[@]}`
7. Why is this Bash-specific? Associative arrays are a bash 4+ feature, not a part of POSIX shell. They won't work in `sh` or older shells. 
8. When should you avoid making a shell script too complex with arrays? When the data/logic needs nested structures, complex data manipulation, or real datatypes; at that point a script is better written in a language like Python. 
=======
=======
>>>>>>> origin/publish
1. What is an associative array?
2. How is it different from an indexed array?
3. How do you declare an associative array in Bash?
4. How do you assign a value to a key?
5. How do you access a value by key?
6. How do you list all keys?
7. Why is this Bash-specific?
8. When should you avoid making a shell script too complex with arrays?
<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish

---

# Exercise 1: Create a simple associative array

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
## Skill being gained

He is learning key/value lookup in Bash.

## Predict before typing

Answer:

```text
What are the keys?
What are the values?
What should ${status_color[error]} produce?
```

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
<<<<<<< HEAD
1. Why did we use `declare -A`? To tell Bash that the array is associative, not indexed. 
2. What is the key in `status_color[error]`? `error`
3. What is the value? `red`
4. Why is this more readable than remembering that index 0 means error? Named keys are self-documenting. You don't need to remember what a numeric position represents. 
=======
=======
>>>>>>> origin/publish
1. Why did we use `declare -A`?
2. What is the key in `status_color[error]`?
3. What is the value?
4. Why is this more readable than remembering that index 0 means error?
<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish

---

# Exercise 2: Loop over keys

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
## Skill being gained

He is learning to inspect all key/value pairs.

## Predict before typing

Answer:

```text
What should ${!status_color[@]} produce?
Will the keys necessarily appear in the order I assigned them?
Should I rely on associative-array order?
```

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
<<<<<<< HEAD
1. What did `${!status_color[@]}` expand to? The list of keys: `error`, `warning`, `ok`.
2. Why did the loop use `"${!status_color[@]}"`? Quoting preserves keys as separate words even if they contain spaces, preventing word-splitting issues. 
3. Why did the value use `${status_color[$key]}`? To look up the value stored under the current key from the loop. 
4. Should a script depend on the printed order of keys? No. Bash doesn't guarantee a consistent order for associative array keys. 
=======
=======
>>>>>>> origin/publish
1. What did `${!status_color[@]}` expand to?
2. Why did the loop use `"${!status_color[@]}"`?
3. Why did the value use `${status_color[$key]}`?
4. Should a script depend on the printed order of keys?
<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish

---

# Exercise 3: Final lab — script report using arrays

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
<<<<<<< HEAD
1. What does the indexed array `servers` hold? A list of server names. 
2. What does the associative array `role` hold? A mapping of each server name to its role. 
3. Why does the loop use `"${servers[@]}"`? To literate over each server name safely, preserving any names with spaces. 
4. Why does the role lookup use `${role[$server]}`? `${role[$server]}`
5. What happens if `servers` contains a server with no role key? The lookup returns an empty string, so the report shows a blank role. 
6. How could you detect that problem? Use `[[ -v "role[$server]" ]]` to check if the key exists before using it. 
=======
=======
>>>>>>> origin/publish
1. What does the indexed array `servers` hold?
2. What does the associative array `role` hold?
3. Why does the loop use `"${servers[@]}"`?
4. Why does the role lookup use `${role[$server]}`?
5. What happens if `servers` contains a server with no role key?
6. How could you detect that problem?
<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish

Try this experiment:

```bash
cp array-report.sh array-report-missing-role.sh
```

Edit the copy and add `cache01` to the `servers` array but not to the `role` array. Then run it.

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
Answer:

```text
What did the missing role look like?
Should the script accept that silently?
What test could detect it?
```

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
---

# Exercise 4: Add validation for missing keys

<<<<<<< HEAD
<<<<<<< HEAD
=======
=======
>>>>>>> origin/publish
## Skill being gained

He is learning that arrays do not remove the need for validation.

## Predict before typing

Answer:

```text
How can I test whether a role exists for a server?
What should the script print when a role is missing?
```

<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
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

<<<<<<< HEAD
<<<<<<< HEAD
1. What does `[[ -v "role[$server]" ]]` test? Whether the key `$server` exists in the `role` associative array. 
2. Why is validation still needed even when using arrays? Arrays do not guarantee data completeness. A missing key can silently produce empty output unless it's explicitly checked. 
3. What would be a better behavior in a real admin script: print `UNKNOWN`, warn, or exit? Why? Depends on severity. Printing `unknown` is fine for a quick report; warning is better for scripts other people run; existing is best when a missing role could cause downstream misconfiguration or automation errors. 
=======
1. What does `[[ -v "role[$server]" ]]` test?
2. Why is validation still needed even when using arrays?
3. What would be a better behavior in a real admin script: print `UNKNOWN`, warn, or exit? Why?
>>>>>>> origin/publish
=======
1. What does `[[ -v "role[$server]" ]]` test?
2. Why is validation still needed even when using arrays?
3. What would be a better behavior in a real admin script: print `UNKNOWN`, warn, or exit? Why?
>>>>>>> origin/publish

---

# Final concept questions

Answer in writing:

<<<<<<< HEAD
<<<<<<< HEAD
1. What problem do arrays solve in Bash scripts? They let you store and manage multiple related values under one variable name instead of using many separate variables. 
2. What is the difference between indexed arrays and associative arrays? Indexed arrays use numeric positions. Associative arrays use string keys. 
3. What does `"${array[@]}"` do? Expands to all elements of the array as separate, individually-quoted words. 
4. What does `${#array[@]}` do? It returns the number of elements in the array. 
5. What does `${!array[@]}` do? Returns all the indexes or keys. 
6. Why can array indexes have gaps? Because you can assign to arbitrary numeric indexes or delete individual elements, leaving non-contagious indexes. 
7. How do you append to an array? `array+=(new-value)`
8. How do you delete one element? `unset array[index_or_key]`
9. Why should array expansions usually be quoted? To prevent word-splitting and globbing, ensuring each element is treated as a single item. 
10. When should you avoid using arrays in a shell script? When the data structure needs are complex at that point a different language is more appropriate. 
=======
=======
>>>>>>> origin/publish
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
<<<<<<< HEAD
>>>>>>> origin/publish
=======
>>>>>>> origin/publish
