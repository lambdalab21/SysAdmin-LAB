# TLCL Chapter 35: Arrays

This guide is for Chapter 35, **Arrays**, from William Shotts's *The Linux Command Line*.

Use this chapter as a practical introduction to Bash arrays, not as a reason to turn shell scripts into Python programs.

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

# Day 2: Array Operations, Loops, Sorting, and Deleting

## Read before exercises

Read Chapter 35 section:

```text
Array Operations
```

---

# After reading: concept questions

Answer without looking back:

1. How do you output every element of an array? Use "${array[@]}".
2. How do you count the number of elements? Use ${#array[@]}
3. How do you show the indexes used by an array? Use ${!array[@]}
4. How do you append a new element? Use array+=(new_element)
5. How do you delete one element? Use unset 'array[index]'
6. How do you delete the whole array? unset array. 
7. Why can array indexes have gaps? Deleting an element removes that index without renumbering the remaining elements. 
8. Why should you preview before using arrays with `rm`, `mv`, `cp`, or `chmod`? Previewing helps prevent accidental changes to the wrong files. This is especially helpful when names contain spaces and/or unexpected values. 

---

# Exercise 1: Count and inspect elements

## Commands

```bash
cd ~/tlcl-ch35-arrays

cat > day2-array-inspect.sh <<'EOF'
#!/usr/bin/env bash

servers=(app01 db01 playground01)

printf 'Number of elements: %s\n' "${#servers[@]}"
printf 'Indexes used: <%s>\n' "${!servers[@]}"

printf 'Elements:\n'
printf '  <%s>\n' "${servers[@]}"
EOF

bash day2-array-inspect.sh
```

## Explain-back

Answer:

1. What does `${#servers[@]}` mean? ${#servers[@]} is the number of elements in servers. 
2. What does `${!servers[@]}` mean? $(!servers[@]) expands to the indexes currently used in servers. 
3. Why is `printf` better than trusting your memory? printf shows the actual array contents and structure instead of relying on assumptions. 

---

# Exercise 2: Loop safely over array elements

## Commands

```bash
cat > day2-loop-array.sh <<'EOF'
#!/usr/bin/env bash

files=("daily report.txt" "error log.txt" "notes.md")

for file in "${files[@]}"; do
    printf 'Would process: <%s>\n' "$file"
done
EOF

bash day2-loop-array.sh
```

## Explain-back

Answer:

1. Why is `"${files[@]}"` quoted? "${files[@]}" is quoted so each array element stays a single item, even if it contains spaces. 
2. Why is `$file` quoted inside the loop? "$file" is quoted so the filename is passed to printf as one value. 
3. What would happen if `daily report.txt` were split into two words? It would become daily and report.txt, which could make the script act on the wrong files. 
4. Why does the script say `Would process` instead of actually changing files? It's a safe preview. It shows what would happen without modifying anything.

---

# Exercise 3: Append and delete elements

## Commands

```bash
cat > day2-append-delete.sh <<'EOF'
#!/usr/bin/env bash

servers=(app01 db01 playground01)

printf 'Original count: %s\n' "${#servers[@]}"
printf 'Original indexes: <%s>\n' "${!servers[@]}"

servers+=(cache01)
printf 'After append count: %s\n' "${#servers[@]}"
printf 'After append indexes: <%s>\n' "${!servers[@]}"
printf 'After append values:\n'
printf '  <%s>\n' "${servers[@]}"

unset 'servers[1]'
printf 'After deleting index 1 count: %s\n' "${#servers[@]}"
printf 'After deleting index 1 indexes: <%s>\n' "${!servers[@]}"
printf 'After deleting index 1 values:\n'
printf '  <%s>\n' "${servers[@]}"
EOF

bash day2-append-delete.sh
```

## Explain-back

Answer:

1. What did `servers+=(cache01)` do? servers+=(cache01) added cache01 as a new final element. 
2. What did `unset 'servers[1]'` do? unset 'servers[1]' removed the element at index 1.  
3. Did the indexes shift after deleting index 1? No. 
4. Why should you not assume the indexes are always continuous? Array elements can be deleted or assigned at specific indexes, leaving unused indexes. 

---

# Exercise 4: Sort an array safely
## Commands

```bash
cat > day2-sort-array.sh <<'EOF'
#!/usr/bin/env bash

names=(zoe alice bob charlie)

printf 'Original:\n'
printf '  <%s>\n' "${names[@]}"

mapfile -t sorted_names < <(printf '%s\n' "${names[@]}" | sort)

printf 'Sorted:\n'
printf '  <%s>\n' "${sorted_names[@]}"
EOF

bash day2-sort-array.sh
```

## Explain-back

Answer:

1. What did `printf '%s\n' "${names[@]}"` produce? It printed each name on its own line
2. What did `sort` do? `sort` arranges those lines alphabetically. 
3. What did `mapfile -t sorted_names` store? It read the sorted lines into the `sorted_names` array, one line per element while removing trailing newline characters. 
4. Why is this more complex than sorting a plain text file? A text file is already lines of text. An array must first be converted into lines, sorted, and read back into an array.

Do not worry if process substitution looks advanced. The main lesson is:

```text
Array values can be sent through text tools, but inspect each stage.
```