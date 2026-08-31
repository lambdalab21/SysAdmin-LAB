# TLCL Chapter 35: Arrays

<<<<<<< HEAD
=======
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

>>>>>>> origin/publish
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

<<<<<<< HEAD
=======
# Before reading: Feynman preview

Explain this before reading:

```text
If I have three servers, I could make three variables:
server1=app01
server2=db01
server3=playground01

But that does not scale well.
An array lets me say:
servers=(app01 db01 playground01)

Now I have one name for the whole list.
```

Do not type yet. First answer:

1. What problem do arrays solve?
2. Why might separate variables like `server1`, `server2`, and `server3` become clumsy?
3. Why might an array be better when looping?

---

>>>>>>> origin/publish
# After reading: concept questions

Answer without looking back:

<<<<<<< HEAD
1. What is an array? A variable that can hold multiple values under one name. 
2. What is an array element? One individual value stored in the array.  
3. What is an array index or subscript? The number that selects a particular element. 
4. What is the first index of a normal Bash indexed array? 0. 
5. How do you assign several values at once? name=(value1 value2 value3)
6. How do you access only the first element? ${array[0]}
7. Why are braces needed in `${array[0]}`? So that bash knows the array name and index; without braces the expansion is ambiguous. 
8. Why should array values often be quoted? To protect spaces and special characters in the values. 
=======
1. What is an array?
2. What is an array element?
3. What is an array index or subscript?
4. What is the first index of a normal Bash indexed array?
5. How do you assign several values at once?
6. How do you access only the first element?
7. Why are braces needed in `${array[0]}`?
8. Why should array values often be quoted?

Do not do the exercises until these are answered.
>>>>>>> origin/publish

---

# Exercise 1: Create and inspect a simple array

<<<<<<< HEAD
=======
## Skill being gained

He is learning to create an indexed array and inspect individual elements.

## Predict before typing

Before typing, answer:

```text
How many values will the array contain?
Which value is at index 0?
Which value is at index 1?
Which value is at index 2?
```

>>>>>>> origin/publish
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

<<<<<<< HEAD
1. Why is the first item index `0`, not `1`? Bash indexed arrays are zero-based by design. 
2. What would `${servers[3]}` print? Nothing. 
3. How would you test that? `printf '<%s>\n' "${servers[3]}`
=======
1. Why is the first item index `0`, not `1`?
2. What would `${servers[3]}` print?
3. How would you test that?
>>>>>>> origin/publish

---

# Exercise 2: Assignment by index
<<<<<<< HEAD
=======

## Skill being gained

He is learning that array elements can be assigned individually.

## Read before exercise

Reread:

```text
Assigning Values to an Array
Accessing Array Elements
```

## Predict before typing

Answer:

```text
If I change servers[1], which element changes?
Will servers[0] change?
Will servers[2] change?
```

>>>>>>> origin/publish
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

<<<<<<< HEAD
1. Which element changed? Index 1. 
2. Which elements stayed the same? Indexes zero and two. 
3. Why is this better than creating new variable names? Related values stay together under one name instead of many separate variables. 
=======
1. Which element changed?
2. Which elements stayed the same?
3. Why is this better than creating new variable names?
>>>>>>> origin/publish

---

# Exercise 3: Values with spaces
<<<<<<< HEAD
=======

## Skill being gained

He is learning why quoting matters.

## Predict before typing

Answer:

```text
If an array element contains a space, what could go wrong when it is not quoted?
What should printf show if the value is preserved correctly?
```

>>>>>>> origin/publish
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

<<<<<<< HEAD
1. Why did the array use quotes around `daily report.txt`? So the space is part of the single element, not a separator. 
2. Why did the expansion use quotes around `${files[0]}`? So the whole element is treated as one argument. 
3. What danger would appear if these were real filenames? Word-splitting or globbing could turn one filename into many wrong arguments. 
=======
1. Why did the array use quotes around `daily report.txt`?
2. Why did the expansion use quotes around `${files[0]}`?
3. What danger would appear if these were real filenames?
>>>>>>> origin/publish

---

# Exercise 4: First look at all elements

<<<<<<< HEAD
=======
## Skill being gained

He is learning that expanding one element and expanding all elements are different operations.

## Predict before typing

Before running, predict the output of each line:

```bash
printf '<%s>\n' "${files[0]}"
printf '<%s>\n' "${files[@]}"
printf '<%s>\n' "${files[*]}"
```

>>>>>>> origin/publish
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

<<<<<<< HEAD
1. What did `${files[0]}` produce? Only the first element. 
2. What did `"${files[@]}"` produce? Each element as a separate word. 
3. What did `"${files[*]}"` produce? All elements joined into one word. 
4. Which form is usually safer when looping over elements? `"${array[0]}"`

=======
1. What did `${files[0]}` produce?
2. What did `"${files[@]}"` produce?
3. What did `"${files[*]}"` produce?
4. Which form is usually safer when looping over elements?

Do not memorize blindly. Explain what Bash gave to `printf`.

---

# Day 1 finish standard

He is done with Day 1 only if he can say:

```text
I can create an indexed array.
I know the first index is 0.
I can access one element with ${array[index]}.
I can expand all elements with "${array[@]}".
I know that quoting matters when values contain spaces.
```

He must also be able to explain why this is dangerous:

```bash
for file in ${files[@]}; do
    echo "$file"
done
```

and why this is safer:

```bash
for file in "${files[@]}"; do
    echo "$file"
done
```
>>>>>>> origin/publish
