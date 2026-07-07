# BashGuide Patterns — Reading Guide Plan

A cleaner phrasing: “Please make a reading guide from BashGuide/Patterns.”

Use this after Shotts Chapter 7 and before deeper regex practice.

Recommended order:

| Session | File | Focus |
|---|---|---|
| 1 | `01_pattern_map_glob_extglob_regex.md` | Pattern map: glob, extended glob, regex, brace expansion |
| 2 | `02_glob_patterns_filename_expansion.md` | Ordinary globs and filename expansion |
| 3 | `03_safe_file_enumeration_and_string_matching.md` | Safe file loops and glob matching in `[[ ... ]]` |
| 4 | `04_extended_globs.md` | Extended globs with `shopt -s extglob` |
| 5 | `05_regex_and_brace_expansion_contrast.md` | Regex and brace expansion contrast |
| 6 | `06_final_lab_and_self_test.md` | Final lab and retention test |

Main skill:

```text
Know whether the shell expands a pattern before the command runs, or whether a tool receives the pattern and interprets it later.
```

Minimum success standard:

```text
He can explain the difference between:

ls *.txt
find . -name "*.txt"
grep '[0-9]' file.txt
[[ $name = *.txt ]]
[[ $line =~ ^[0-9]+$ ]]
echo file{1..3}.txt
```

He should not move on until he can explain what expands, who expands it, and what arguments the command actually receives.
