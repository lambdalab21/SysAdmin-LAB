# TLCL Chapter 35: Arrays

# Day 1: Indexed Arrays, Creation, Access, and Expansion

## Read before exercises

Read Chapter 35 from the beginning through these sections:

```text
What Are Arrays?
Creating an Array
Assigning Values to an Array
Accessing Array Elements
```

## What he should gain

He should gain this mental model:

```text
A normal variable holds one value.
An array can hold many values under one name.
Each value is selected by an index.
```

He is learning how Bash can keep a small list of related values without creating many separate variable names.

---

# After reading: concept questions

Answer without looking back:

1. What is an array? A variable that can hold multiple values under one name. 
2. What is an array element? One individual value stored in the array.  
3. What is an array index or subscript? The number that selects a particular element. 
4. What is the first index of a normal Bash indexed array? 0. 
5. How do you assign several values at once? name=(value1 value2 value3)
6. How do you access only the first element? ${array[0]}
7. Why are braces needed in `${array[0]}`? So that bash knows the array name and index; without braces the expansion is ambiguous. 
8. Why should array values often be quoted? To protect spaces and special characters in the values. 

---

# Exercise 1: Create and inspect a simple array

## Commands

```bash
cd ~/tlcl-ch35-arrays

cat > day1-array-basic.sh <<'EOF'
#!/usr/bin/env bash

servers=(app01 db01 playground01)

printf 'Index 0: <%s>\n' "${servers[0]}"
printf 'Index 1: <%s>\n' "${servers[1]}"
printf 'Index 2: <%s>\n' "${servers[2]}"
EOF

bash day1-array-basic.sh
```

## Explain-back

Explain:

```text
servers is the array name.
0, 1, and 2 are indexes.
${servers[0]} expands to the first element.
```

Then answer:

1. Why is the first item index `0`, not `1`? Bash indexed arrays are zero-based by design. 
2. What would `${servers[3]}` print? Nothing. 
3. How would you test that? `printf '<%s>\n' "${servers[3]}`

---

# Exercise 2: Assignment by index
## Commands

```bash
cat > day1-array-assignment.sh <<'EOF'
#!/usr/bin/env bash

servers=(app01 db01 playground01)
servers[1]=cache01

printf '0=<%s>\n' "${servers[0]}"
printf '1=<%s>\n' "${servers[1]}"
printf '2=<%s>\n' "${servers[2]}"
EOF

bash day1-array-assignment.sh
```

## Explain-back

Answer:

1. Which element changed? Index 1. 
2. Which elements stayed the same? Indexes zero and two. 
3. Why is this better than creating new variable names? Related values stay together under one name instead of many separate variables. 

---

# Exercise 3: Values with spaces
## Commands

```bash
cat > day1-array-spaces.sh <<'EOF'
#!/usr/bin/env bash

files=("daily report.txt" "error log.txt" "notes.md")

printf 'First file: <%s>\n' "${files[0]}"
printf 'Second file: <%s>\n' "${files[1]}"
printf 'Third file: <%s>\n' "${files[2]}"
EOF

bash day1-array-spaces.sh
```

## Explain-back

Answer:

1. Why did the array use quotes around `daily report.txt`? So the space is part of the single element, not a separator. 
2. Why did the expansion use quotes around `${files[0]}`? So the whole element is treated as one argument. 
3. What danger would appear if these were real filenames? Word-splitting or globbing could turn one filename into many wrong arguments. 

---

# Exercise 4: First look at all elements

## Commands

```bash
cat > day1-array-all-elements.sh <<'EOF'
#!/usr/bin/env bash

files=("daily report.txt" "error log.txt" "notes.md")

echo 'One element:'
printf '<%s>\n' "${files[0]}"

echo 'All elements with @:'
printf '<%s>\n' "${files[@]}"

echo 'All elements with *:'
printf '<%s>\n' "${files[*]}"
EOF

bash day1-array-all-elements.sh
```

## Explain-back

Answer:

1. What did `${files[0]}` produce? Only the first element. 
2. What did `"${files[@]}"` produce? Each element as a separate word. 
3. What did `"${files[*]}"` produce? All elements joined into one word. 
4. Which form is usually safer when looping over elements? `"${array[0]}"`

