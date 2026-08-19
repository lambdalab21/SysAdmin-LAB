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

# Day 2: Array Operations, Loops, Sorting, and Deleting

## Read before exercises

Read Chapter 35 section:

```text
Array Operations
```

Pay special attention to subsections like these if they appear in your edition:

```text
Outputting the Entire Contents of an Array
Determining the Number of Array Elements
Finding the Subscripts Used by an Array
Adding Elements to the End of an Array
Sorting an Array
Deleting an Array
```

## What he should gain

He should gain this practical skill:

```text
Use arrays as safe lists in scripts.
Inspect the list before acting on it.
Loop over each element without breaking filenames that contain spaces.
```

---

# Before reading: Feynman preview

Explain this before reading:

```text
Having an array is not enough.
I need to know how many boxes it has, what their labels are, how to add a box, how to remove one, and how to loop over the boxes safely.
```

Do not type yet. First answer:

1. Why is `"${array[@]}"` useful in a `for` loop?
2. Why might counting array elements be useful?
3. Why might seeing the used indexes matter?

---

# After reading: concept questions

Answer without looking back:

1. How do you output every element of an array?
2. How do you count the number of elements?
3. How do you show the indexes used by an array?
4. How do you append a new element?
5. How do you delete one element?
6. How do you delete the whole array?
7. Why can array indexes have gaps?
8. Why should you preview before using arrays with `rm`, `mv`, `cp`, or `chmod`?

---

# Exercise 1: Count and inspect elements

## Skill being gained

He is learning to inspect array contents before using them.

## Predict before typing

Answer:

```text
How many elements are in the array?
What indexes are used?
What should each printf line show?
```

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

1. What does `${#servers[@]}` mean?
2. What does `${!servers[@]}` mean?
3. Why is `printf` better than trusting your memory?

---

# Exercise 2: Loop safely over array elements

## Skill being gained

He is learning the safest common loop pattern for arrays.

## Read before exercise

Reread the parts on outputting array contents and array operations.

## Predict before typing

Answer:

```text
How many loop passes should happen?
What value should item have on the first pass?
What value should item have on the last pass?
```

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

1. Why is `"${files[@]}"` quoted?
2. Why is `$file` quoted inside the loop?
3. What would happen if `daily report.txt` were split into two words?
4. Why does the script say `Would process` instead of actually changing files?

---

# Exercise 3: Append and delete elements

## Skill being gained

He is learning how arrays change over time.

## Predict before typing

Answer:

```text
How many elements before append?
How many after append?
What happens after deleting index 1?
Will the remaining elements shift automatically?
```

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

1. What did `servers+=(cache01)` do?
2. What did `unset 'servers[1]'` do?
3. Did the indexes shift after deleting index 1?
4. Why should you not assume the indexes are always continuous?

---

# Exercise 4: Sort an array safely

## Skill being gained

He is learning that arrays can be transformed using text tools, but the transformation must be inspected.

## Predict before typing

Answer:

```text
What order are the names in now?
What order should sort produce?
Will the original array change automatically, or do we need to store the result?
```

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

1. What did `printf '%s\n' "${names[@]}"` produce?
2. What did `sort` do?
3. What did `mapfile -t sorted_names` store?
4. Why is this more complex than sorting a plain text file?

Do not worry if process substitution looks advanced. The main lesson is:

```text
Array values can be sent through text tools, but inspect each stage.
```

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
I can count array elements with ${#array[@]}.
I can see used indexes with ${!array[@]}.
I can loop safely with for item in "${array[@]}".
I can append with array+=(value).
I can delete an element with unset 'array[index]'.
I know indexes may have gaps.
I preview array contents before using them in dangerous commands.
```
