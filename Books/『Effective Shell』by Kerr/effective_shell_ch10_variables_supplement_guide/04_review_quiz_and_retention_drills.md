# Effective Shell Ch. 10 — Review Quiz and Retention Drills

Use this one day after finishing Day 3, then again one week later.

Do not look at notes first.

---

# Part 1: Quick concept quiz

Answer in writing.

1. What does `name=value` do?
2. Why is `name = value` wrong in Bash?
3. What does `$name` do?
4. Why might `${name}` be clearer than `$name`?
5. What does `export name` do?
6. What is the difference between a shell variable and an environment variable?
7. Why is `"$name"` usually safer than `$name`?
8. What two things can happen after unquoted variable expansion?
9. What does `$(hostname)` do?
10. What does `read -r project` do?
11. What does `$((count + 1))` do?
12. What does `"${array[@]}"` mean?
13. What does `${title:-Default}` mean?
14. When is Bash the wrong tool for data manipulation?

---

# Part 2: Predict the output

Predict before running.

```bash
x="a b"
printf '<%s>\n' $x
printf '<%s>\n' "$x"
```

```bash
name="app01"
printf '<%s>\n' "$name_backup"
printf '<%s>\n' "${name}_backup"
```

```bash
items=("one file" "two file")
printf '<%s>\n' ${items[@]}
printf '<%s>\n' "${items[@]}"
```

```bash
unset title
printf '<%s>\n' "${title:-Untitled}"
title="Real Title"
printf '<%s>\n' "${title:-Untitled}"
```

Then run and compare.

---

# Part 3: Fix the unsafe script

This script is intentionally unsafe or sloppy.

```bash
#!/usr/bin/env bash

project=$1
output=$project report.txt
files=*.txt

echo Project: $project
echo Output: $output
echo Files: $files
```

Rewrite it safely.

Requirements:

```text
Use quotes where needed.
Use braces where helpful.
Use printf instead of echo for inspection.
Do not accidentally expand globs unless that is intended.
```

Possible safer direction:

```bash
#!/usr/bin/env bash

project="$1"
output="${project}-report.txt"
pattern="*.txt"

printf 'Project: <%s>\n' "$project"
printf 'Output: <%s>\n' "$output"
printf 'Pattern: <%s>\n' "$pattern"
printf 'Matching files:\n'
printf '  <%s>\n' *.txt
```

Explain why each quote exists.

---

# Part 4: Mini-script from scratch

Write a script named:

```text
machine-note.sh
```

Requirements:

```text
Store hostname in a variable.
Store date in a variable.
Read a short note from the user.
Print all values using printf.
Use quotes around variable expansions.
```

Test with a note containing spaces.

Example input:

```text
checked nginx config
```

After running, explain:

1. Which variables came from commands?
2. Which variable came from user input?
3. Which expansions needed quotes?
4. How could this become part of a system report script?

---

# Part 5: Final explain-back

Explain this to a younger student:

```text
A variable is not magic storage. It is text that the shell may expand into a command line. Because the shell can split and glob expanded text, I usually quote variable expansions. If a child process needs the variable, I export it. If I need command output, I use command substitution. If I need a small list, I can use an array, but I must expand it safely.
```

If he cannot explain this clearly, repeat Day 1 and Day 2 drills.
