# Day 1: `read`, Variables, Options, `IFS`, and Here Strings

## Read before exercises

Read Chapter 28 from the beginning through the main section:

```text
read—Read Values from Standard Input
```

Include the material on:

```text
read syntax
reading into variables
REPLY
read options
IFS
here strings
“You Can’t Pipe read”
```

Do **not** start typing yet.

## What he should gain from this reading

He should gain this mental model:

```text
read takes one line from standard input.
Then Bash assigns that input to one or more variables.
The script can make decisions using those variables.
```

He is not learning “how to ask a question.”

He is learning:

```text
How does data enter a script?
Where does that data go?
What can go wrong before I use it?
```

---

# Before-reading Feynman preview

Explain this in your own words before reading:

```text
A command-line script normally talks by printing to stdout.
With read, the script can also listen from stdin.
```

Analogy:

```text
stdout = the script speaking
stdin  = the script listening
read   = the script writing down what it heard
```

---

# After-reading concept questions

Answer without looking back:

1. What does `read` read by default?
2. What does `read name` do?
3. What happens if `read` is used without a variable name?
4. What is `REPLY`?
5. What does `read -p` do?
6. Why is `read -r` usually safer than plain `read`?
7. What does `IFS` control?
8. What is a here string, `<<<`?
9. Why does `echo foo | read var` not work the way beginners expect?
10. What does “subshell” mean in this context?

Do not continue until these are answered in writing.

---

# Exercise 1: Read one value deliberately

## Section to read before this exercise

Reread the first part of:

```text
read—Read Values from Standard Input
```

Focus on simple variable assignment.

## Skill being gained

He is learning that keyboard input becomes variable content.

## Do not type yet

Predict:

```text
Prompt shown:
Variable assigned:
Expected output if I type: app01
Expected output if I just press Enter:
```

## Create the script

```bash
cd ~/tlcl-ch28-input

cat > read-one.sh <<'SCRIPT'
#!/usr/bin/env bash

printf 'Enter hostname > '
read hostname
printf 'hostname=<%s>\n' "$hostname"
SCRIPT

bash read-one.sh
```

Run it twice:

```text
First input: app01
Second input: press Enter with no text
```

## Explain-back

Answer:

1. What variable was assigned?
2. Was empty input accepted?
3. Is that good or bad?
4. Why should later scripts validate input?

---

# Exercise 2: Use `read -r -p`

## Section to read before this exercise

Read the options table or options discussion for `read`.

Focus on:

```text
-p
-r
```

## Skill being gained

He is learning to prompt clearly and preserve literal input.

## Do not type yet

Predict:

```text
What does -p change?
What does -r protect against?
Why should the variable still be quoted when printed?
```

## Create the script

```bash
cat > read-prompt.sh <<'SCRIPT'
#!/usr/bin/env bash

read -r -p 'Enter a path > ' path
printf 'path=<%s>\n' "$path"
SCRIPT

bash read-prompt.sh
```

Try these inputs:

```text
/home/student
/tmp/my file.txt
C:\Users\student
```

## Explain-back

Answer:

1. Why did we use `-r`?
2. Why did we quote `"$path"`?
3. What could go wrong if the path contains spaces?
4. Is this script validating the path yet?

---

# Exercise 3: Read multiple values

## Section to read before this exercise

Reread the part of the `read` section showing multiple variable names.

## Skill being gained

He is learning that input splitting matters.

## Do not type yet

Predict what happens if the user enters:

```text
alpha beta gamma
```

for this command:

```bash
read first second third
```

Then predict what happens if the user enters:

```text
alpha beta gamma delta epsilon
```

## Create the script

```bash
cat > read-many.sh <<'SCRIPT'
#!/usr/bin/env bash

read -r -p 'Enter three words > ' first second third
printf 'first=<%s>\n' "$first"
printf 'second=<%s>\n' "$second"
printf 'third=<%s>\n' "$third"
SCRIPT

bash read-many.sh
```

Try:

```text
one two three
one two three four five
one
```

## Explain-back

Answer:

1. What happens when there are exactly enough words?
2. What happens when there are too many words?
3. What happens when there are too few words?
4. Why does this matter when reading human input?

---

# Exercise 4: Use `REPLY`

## Section to read before this exercise

Reread the part about using `read` without variable names.

## Skill being gained

He is learning Bash’s default input variable.

## Do not type yet

Predict:

```text
If read has no variable name, where does the input go?
Is REPLY a good name for a serious script variable?
```

## Create the script

```bash
cat > read-reply.sh <<'SCRIPT'
#!/usr/bin/env bash

read -r -p 'Type anything > '
printf 'REPLY=<%s>\n' "$REPLY"
SCRIPT

bash read-reply.sh
```

## Explain-back

Answer:

1. When is `REPLY` useful?
2. When is a named variable clearer?
3. Why might `read answer` be better than plain `read` in a larger script?

---

# Exercise 5: Parse colon-separated data with `IFS`

## Section to read before this exercise

Read the part about `IFS` and the example using colon-separated data.

## Skill being gained

He is learning that `read` does not just “take input.” It can split input according to a delimiter.

## Do not type yet

Predict how this line should split:

```text
student:x:1000:1000:Student User:/home/student:/bin/bash
```

into:

```text
user
pw
uid
gid
name
home
shell
```

## Create the script

```bash
cat > read-ifs.sh <<'SCRIPT'
#!/usr/bin/env bash

file_info='student:x:1000:1000:Student User:/home/student:/bin/bash'

IFS=':' read -r user pw uid gid name home shell <<< "$file_info"

printf 'user=<%s>\n' "$user"
printf 'uid=<%s>\n' "$uid"
printf 'name=<%s>\n' "$name"
printf 'home=<%s>\n' "$home"
printf 'shell=<%s>\n' "$shell"
SCRIPT

bash read-ifs.sh
```

## Explain-back

Answer:

1. What did `IFS=':'` do?
2. Why was the `IFS` assignment placed before `read`?
3. What did `<<< "$file_info"` do?
4. Why did we not use `echo "$file_info" | read ...`?

---

# Exercise 6: Demonstrate the pipe problem

## Section to read before this exercise

Read or reread:

```text
You Can’t Pipe read
```

## Skill being gained

He is learning a subtle shell behavior: pipelines may create subshells.

## Do not type yet

Predict:

```text
Will x still have the value foo after the pipeline finishes?
Why or why not?
```

## Create the script

```bash
cat > pipe-read-test.sh <<'SCRIPT'
#!/usr/bin/env bash

echo 'foo' | read -r x
printf 'After pipeline: x=<%s>\n' "$x"

read -r y <<< 'bar'
printf 'After here string: y=<%s>\n' "$y"
SCRIPT

bash pipe-read-test.sh
```

## Explain-back

Answer:

1. Why was `x` empty?
2. Why did `y` work?
3. What is a subshell?
4. Why does this matter in real scripts?

---

# Day 1 final checkpoint

He may stop Day 1 only if he can explain:

```text
read stores input in variables.
-r prevents backslash interpretation.
-p displays a prompt.
REPLY is used when no variable name is supplied.
IFS controls splitting.
A here string can feed one string to read.
A pipeline can cause read to happen in a subshell, losing the assigned variable.
```

Final written answer:

```text
The most dangerous misunderstanding from Day 1 is:

The habit I will use to avoid it is:
```
