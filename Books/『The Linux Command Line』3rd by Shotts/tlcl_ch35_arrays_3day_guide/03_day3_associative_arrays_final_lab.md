# TLCL Chapter 35: Arrays

This guide is for Chapter 35, **Arrays**, from William Shotts's *The Linux Command Line*.

Use this chapter as a practical introduction to Bash arrays, not as a reason to turn shell scripts into Python programs.

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

# After reading: concept questions

Answer without looking back:

1. What is an associative array? An array that uses named keys instead of numbers to index values. 
2. How is it different from an indexed array? Indexed arrays use sequential integers as labels; associative arrays use arbitrary strings as labels. 
3. How do you declare an associative array in Bash? `declare -A array_name`
4. How do you assign a value to a key? `array_name[key]=value`
5. How do you access a value by key? `${array_name[key]}`
6. How do you list all keys? `${!array_name[@]}`
7. Why is this Bash-specific? Associative arrays are a bash 4+ feature, not a part of POSIX shell. They won't work in `sh` or older shells. 
8. When should you avoid making a shell script too complex with arrays? When the data/logic needs nested structures, complex data manipulation, or real datatypes; at that point a script is better written in a language like Python. 

---

# Exercise 1: Create a simple associative array

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

1. Why did we use `declare -A`? To tell Bash that the array is associative, not indexed. 
2. What is the key in `status_color[error]`? `error`
3. What is the value? `red`
4. Why is this more readable than remembering that index 0 means error? Named keys are self-documenting. You don't need to remember what a numeric position represents. 

---

# Exercise 2: Loop over keys

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

1. What did `${!status_color[@]}` expand to? The list of keys: `error`, `warning`, `ok`.
2. Why did the loop use `"${!status_color[@]}"`? Quoting preserves keys as separate words even if they contain spaces, preventing word-splitting issues. 
3. Why did the value use `${status_color[$key]}`? To look up the value stored under the current key from the loop. 
4. Should a script depend on the printed order of keys? No. Bash doesn't guarantee a consistent order for associative array keys. 

---

# Exercise 3: Final lab — script report using arrays

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

1. What does the indexed array `servers` hold? A list of server names. 
2. What does the associative array `role` hold? A mapping of each server name to its role. 
3. Why does the loop use `"${servers[@]}"`? To literate over each server name safely, preserving any names with spaces. 
4. Why does the role lookup use `${role[$server]}`? `${role[$server]}`
5. What happens if `servers` contains a server with no role key? The lookup returns an empty string, so the report shows a blank role. 
6. How could you detect that problem? Use `[[ -v "role[$server]" ]]` to check if the key exists before using it. 

Try this experiment:

```bash
cp array-report.sh array-report-missing-role.sh
```

Edit the copy and add `cache01` to the `servers` array but not to the `role` array. Then run it.

---

# Exercise 4: Add validation for missing keys

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

1. What does `[[ -v "role[$server]" ]]` test? Whether the key `$server` exists in the `role` associative array. 
2. Why is validation still needed even when using arrays? Arrays do not guarantee data completeness. A missing key can silently produce empty output unless it's explicitly checked. 
3. What would be a better behavior in a real admin script: print `UNKNOWN`, warn, or exit? Why? Depends on severity. Printing `unknown` is fine for a quick report; warning is better for scripts other people run; existing is best when a missing role could cause downstream misconfiguration or automation errors. 

---

# Final concept questions

Answer in writing:

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