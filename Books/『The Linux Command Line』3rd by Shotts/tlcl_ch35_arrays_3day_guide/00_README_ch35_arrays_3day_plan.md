# TLCL Chapter 35: Arrays — Three-Day Guide

Chapter 35 is best split into three days.

| Day | File | Read before exercises | Main gain |
|---|---|---|---|
| Day 1 | `01_day1_indexed_arrays_creation_access_expansion.md` | Start of Chapter 35 through `Creating an Array`, `Assigning Values to an Array`, and `Accessing Array Elements` | Understand what arrays are, how to create them, and how to access individual elements safely |
| Day 2 | `02_day2_array_operations_loops_sorting_deleting.md` | `Array Operations` and its subsections | Use arrays in loops, inspect all elements, count elements, see indexes, append, sort, and delete |
| Day 3 | `03_day3_associative_arrays_final_lab.md` | `Associative Arrays` and `Summing Up` | Use key/value arrays carefully and decide when Bash arrays are useful |

Core habit:

```text
array name
→ index or key
→ expected expansion
→ quoted or unquoted?
→ inspect with printf
→ use in a loop or command
→ explain what Bash produced
```

Final standard:

```text
He can create an indexed array, access one element, expand all elements safely, loop over elements, count elements, inspect indexes, append/delete values, create an associative array, and explain why quoted array expansion matters.
```

Bash warning:

```text
Arrays are Bash features.
Do not assume the same script will work in plain POSIX sh.
Use #!/usr/bin/env bash for these scripts.
```
