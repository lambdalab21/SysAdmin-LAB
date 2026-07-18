# Day 2: Running Scripts, Shebangs, Sourcing, and Installing

## Read before exercises

Read Effective Shell Ch. 9 sections:

```text
Running a Shell Script
Using Shebangs
Shebangs - Dealing with Paths
Sourcing Shell Scripts
Dot Sourcing
Installing Your Script
Summary
```

## What he should gain

He should learn the difference between these actions:

```text
Run a script with bash.
Run an executable script by path.
Use a shebang to choose the interpreter.
Source a script into the current shell.
Install a script so PATH can find it.
```

This is mostly a refresh of TLCL Ch. 24, but Effective Shell adds useful emphasis on `/usr/bin/env`, sourcing, and installing a script as a local command.

## Feynman analogy

Running a script is like sending instructions to a helper in another room.

```text
bash script.sh
./script.sh
```

The helper follows the instructions, but changes they make to their room do not change your room.

Sourcing a script is different. It is like reading the instructions aloud in your own room.

```text
source script.sh
. script.sh
```

Now changes affect your current shell.

---

# After reading: concept questions

Answer without looking back:

1. What does it mean to run a script with `bash script.sh`?
2. What does `chmod +x` change?
3. Why do we run executable scripts by path, such as `./script.sh` or `~/scripts/script.sh`?
4. What is a shebang?
5. Why can `#!/usr/bin/env bash` be more portable than hardcoding a Bash path?
6. What is the difference between executing and sourcing a script?
7. Why can sourcing be dangerous if you do it carelessly?
8. What is dot sourcing?
9. What does `$PATH` do when you type a command name?
10. Why is `/usr/local/bin` or `~/.local/bin` used for local commands instead of `/usr/bin`?

Do not do the exercises until these are answered.

---

# Exercise 1: Add a shebang and run by path

## Skill being gained

He is learning how the operating system knows which interpreter should run the script.

## Read before exercise

Reread:

```text
Running a Shell Script
Using Shebangs
Shebangs - Dealing with Paths
```

## Prediction

Before editing, answer:

```text
What will happen if a script is executable but has no shebang?
What program do I want to run this script?
Why choose /usr/bin/env bash?
```

## Add the shebang

```bash
cat > ~/scripts/common.v1.sh <<'EOF'
#!/usr/bin/env bash
# common.v1.sh - show the most common recent shell commands

# Print the report title.
echo "common commands:"

# Count repeated recent shell commands and show the top ten.
tail ~/.bash_history -n 1000 \
    | sort \
    | uniq -c \
    | sed 's/^ *//' \
    | sort -n -r \
    | head -n 10
EOF
```

Make executable:

```bash
chmod +x ~/scripts/common.v1.sh
```

Run by path:

```bash
~/scripts/common.v1.sh
```

Inspect permissions:

```bash
ls -l ~/scripts/common.v1.sh
```

## Explain-back

Answer:

1. What changed after `chmod +x`?
2. What did the shebang tell the system?
3. Why did we use a path to run the script?
4. Why might `#!/usr/bin/env bash` be safer across machines than `#!/usr/bin/bash`?

---

# Exercise 2: Executing vs sourcing

## Skill being gained

He is learning process/environment boundaries.

## Read before exercise

Reread:

```text
Sourcing Shell Scripts
Dot Sourcing
```

## Prediction

Before typing, answer:

```text
If a script changes a variable, will my current shell see the change after I execute it?
If I source it, will my current shell see the change?
```

## Create the test script

```bash
cat > ~/scripts/set-lab-editor.sh <<'EOF'
#!/usr/bin/env bash

EDITOR=nano
echo "Inside script: EDITOR=$EDITOR"
EOF

chmod +x ~/scripts/set-lab-editor.sh
```

Check current value:

```bash
echo "Before: EDITOR=$EDITOR"
```

Execute it:

```bash
~/scripts/set-lab-editor.sh
echo "After execute: EDITOR=$EDITOR"
```

Source it:

```bash
source ~/scripts/set-lab-editor.sh
echo "After source: EDITOR=$EDITOR"
```

Optional reset:

```bash
unset EDITOR
```

## Explain-back

Answer:

1. What happened after executing the script?
2. What happened after sourcing the script?
3. Why are shell startup files sourced instead of executed?
4. Why should you not source random scripts from the internet?

---

# Exercise 3: Install a script as a local command

## Skill being gained

He is learning that commands are found through `$PATH`.

## Read before exercise

Reread:

```text
Installing Your Script
```

## Safer user-local version

Instead of using `/usr/local/bin`, use the user-owned directory:

```bash
mkdir -p ~/.local/bin
```

Check whether it is already in PATH:

```bash
printf '%s\n' "$PATH" | tr ':' '\n' | grep -x "$HOME/.local/bin"
```

If it is not listed, add this to `~/.bashrc` later, not blindly during the exercise:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

For the current shell only, run:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Create a symlink:

```bash
ln -sf ~/scripts/common.v1.sh ~/.local/bin/common
```

Check what command will run:

```bash
type common
```

Run:

```bash
common
```

## Explain-back

Answer:

1. What is the difference between the script file and the symlink?
2. Why does `common` work without typing the full path?
3. What role did `$PATH` play?
4. Why is `~/.local/bin` safer for a student than `/usr/local/bin`?
5. What could go wrong if two directories in PATH contain a command named `common`?

---

# Day 2 finish standard

He is done with Day 2 only if he can say:

```text
I can run a script explicitly with bash.
I can make a script executable and run it by path.
I can explain the shebang.
I can explain why /usr/bin/env bash is useful.
I can explain executing vs sourcing.
I can install a personal script through ~/.local/bin and PATH.
```
