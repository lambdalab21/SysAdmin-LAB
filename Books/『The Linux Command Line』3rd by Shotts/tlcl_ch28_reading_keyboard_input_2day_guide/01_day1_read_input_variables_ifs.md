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

---

# After-reading concept questions

Answer without looking back:

1. What does `read` read by default?  Read reads a line from the standard input. 
2. What does `read name` do? Read name reads a line and assigns it to the variable "name"
3. What happens if `read` is used without a variable name? It assigns the input to the special value REPLY. 
4. What is `REPLY`? REPLY is the default variable read uses when no variable is given. 
5. What does `read -p` do? read -p prints a prompt before reading input. 
6. Why is `read -r` usually safer than plain `read`? read -r prevents backlash. 
7. What does `IFS` control? IFS controls how read splits input into fields. 
8. What is a here string, `<<<`? A here string feeds a string as standard input to a command. 
9. Why does `echo foo | read var` not work the way beginners expect? The pipe creates a subshell; read runs in a child process so that the variable doesn't affect the parent shell. 
10. What does “subshell” mean in this context? Subshell is a child shell process. Changes inside it don't persist in the parent shell. 

---

# Exercise 1: Read one value deliberately

## Section to read before this exercise

Reread the first part of:

```text
read—Read Values from Standard Input
```

Focus on simple variable assignment.

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

1. What variable was assigned? The hostname variable. 
2. Was empty input accepted? Yes, empty input was accepted. 
3. Is that good or bad? Bad. 
4. Why should later scripts validate input? To ensure data is valid, non-empty, and in expected format before use. 

---

# Exercise 2: Use `read -r -p`

## Section to read before this exercise

Read the options table or options discussion for `read`.

Focus on:

```text
-p
-r
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

1. Why did we use `-r`? To treat backslashes literally. 
2. Why did we quote `"$path"`? To prevent word splitting and globbing on the variable
3. What could go wrong if the path contains spaces? Without quotes, spaces would break the path into multiple words or cause errors. 
4. Is this script validating the path yet? No, it only reads input. No validation is performed. 

---

# Exercise 3: Read multiple values

## Section to read before this exercise

Reread the part of the `read` section showing multiple variable names.

for this command:

```bash
read first second third
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

1. What happens when there are exactly enough words? Each word is assigned to the corresponding variable. 
2. What happens when there are too many words? Extra words are assigned to the last variable. 
3. What happens when there are too few words? remaining variables are set to be empty. 
4. Why does this matter when reading human input? Human input is unpredictable in both count and format. Scripts must handle partial or excess data well. 

---

# Exercise 4: Use `REPLY`

## Section to read before this exercise

Reread the part about using `read` without variable names.

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

1. When is `REPLY` useful? REPLY is useful for quick, one-off or throwaway input. 
2. When is a named variable clearer? Named variables are clearer for complex scripts and self-documenting. 
3. Why might `read answer` be better than plain `read` in a larger script? Named variables make code more readable and maintainable. 

---

# Exercise 5: Parse colon-separated data with `IFS`

## Section to read before this exercise

Read the part about `IFS` and the example using colon-separated data.

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

1. What did `IFS=':'` do? It changed the field separator to : so read splits on colons. 
2. Why was the `IFS` assignment placed before `read`? So the custom IFS applies only to that read command. 
3. What did `<<< "$file_info"` do? It supplied the string as input to read without a pipe. 
4. Why did we not use `echo "$file_info" | read ...`? Piping would run read in a subshell, so values wouldn't be in the main shell. 

---

# Exercise 6: Demonstrate the pipe problem

## Section to read before this exercise

Read or reread:

```text
You Can’t Pipe read
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

1. Why was `x` empty? Because the pipeline ran read in a subshell. 
2. Why did `y` work? Here, string runs in the current shell so the variable is set. 
3. What is a subshell? A child shell process whose environment changes do not affect the parent. 
4. Why does this matter in real scripts? It explain why many read patterns fail with pipes.  
