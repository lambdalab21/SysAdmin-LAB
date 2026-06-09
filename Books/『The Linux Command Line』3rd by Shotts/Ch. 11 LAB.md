# Chapter 11 experiments: The environment

## One-time setup

Log in to `playground01` as the regular user.

Create a backup of the shell startup file before editing anything:

```bash
cp ~/.bashrc ~/.bashrc.backup-ch11
```

Create a safe working directory:

```bash
mkdir -p ~/ch11-experiments/bin
cd ~/ch11-experiments
```

---

# Section 1: What is stored in the environment?

## Experiment 1.1: Compare `printenv` and `set`

Run:

```bash
printenv | less
```

Then:

```bash
set | less
```

Search inside `less` by typing:

```text
/HOME
```

Exit with:

```text
q
```

### Questions

1. Which command produces more output?
    
2. Does `set` show shell functions and shell variables?
    
3. Does `printenv` focus on exported environment variables?
    

### Lesson

The shell maintains more information than child programs receive. `set` shows the broader shell state. `printenv` shows the exported environment.

---

## Experiment 1.2: Inspect important variables

Run:

```bash
echo "$USER"
echo "$HOME"
echo "$SHELL"
echo "$PWD"
echo "$OLDPWD"
echo "$PATH"
```

Then change directories:

```bash
cd /tmp
echo "$PWD"
echo "$OLDPWD"
cd -
echo "$PWD"
echo "$OLDPWD"
```

### Questions

1. Which variable changed when you ran `cd`?
    
2. Which variable remembered the previous directory?
    
3. What does `cd -` do?
    

### Lesson

Variables are not abstract theory. Programs and shell features use them constantly.

---

## Experiment 1.3: Examine `PATH` as a list

Run:

```bash
echo "$PATH"
```

Now make the output easier to read:

```bash
echo "$PATH" | tr ':' '\n'
```

Check where commands come from:

```bash
type ls
type grep
type cd
type echo
```

Also run:

```bash
which ls
```

### Questions

1. What separates directories in `PATH`?
    
2. Is `cd` an external executable?
    
3. Is `ls` found through `PATH`?
    
4. Why is `type` often more informative than `which`?
    

### Lesson

`PATH` is an ordered search list. Bash also knows about aliases, functions, keywords, and built-ins, so not every command is an executable file.

---

## Experiment 1.4: Create a shell variable

Run:

```bash
ANIMAL=fox
echo "$ANIMAL"
```

Inspect it:

```bash
set | grep '^ANIMAL='
```

Now check the exported environment:

```bash
printenv ANIMAL
```

### Expected result

```bash
echo "$ANIMAL"
```

prints:

```text
fox
```

But:

```bash
printenv ANIMAL
```

prints nothing.

### Lesson

A shell variable is not automatically an environment variable.

---

## Experiment 1.5: Test inheritance by a child process

Continue from the previous experiment:

```bash
ANIMAL=fox
bash -c 'echo "child sees: $ANIMAL"'
```

Now export it:

```bash
export ANIMAL
bash -c 'echo "child sees: $ANIMAL"'
```

### Expected result

Before `export`:

```text
child sees:
```

After `export`:

```text
child sees: fox
```

### Lesson

Child processes inherit exported variables. They do not inherit ordinary shell variables.

---

## Experiment 1.6: Test whether a child can modify its parent

Run:

```bash
COLOR=blue
bash -c 'COLOR=red; echo "child: $COLOR"'
echo "parent: $COLOR"
```

### Expected result

```text
child: red
parent: blue
```

### Question

Why did the parent shell keep the value `blue`?

### Lesson

Environment inheritance flows from parent to child. A child process does not normally rewrite the parent shell’s variables.

---

# Section 2: How is the environment established?

## Experiment 2.1: Observe a clean Bash shell

Run:

```bash
bash --noprofile --norc
```

Inside the new shell, run:

```bash
echo "PS1=$PS1"
alias
exit
```

Now run:

```bash
alias
```

again in the normal shell.

### Questions

1. Did the clean shell load your normal aliases?
    
2. Did the prompt look different?
    
3. What do `--noprofile` and `--norc` prevent?
    

### Lesson

Much of the familiar interactive-shell behavior is configured by startup files.

---

## Experiment 2.2: Prove that `.bashrc` is read by an interactive shell

Add a harmless marker to `.bashrc`:

```bash
echo 'echo "[.bashrc loaded]"' >> ~/.bashrc
```

Start a new interactive Bash shell:

```bash
bash
```

You should see:

```text
[.bashrc loaded]
```

Exit:

```bash
exit
```

Now remove the test line:

```bash
sed -i '/\[.bashrc loaded\]/d' ~/.bashrc
```

### Questions

1. When did the message appear?
    
2. Why did it not appear merely when you edited the file?
    
3. What kind of shell read `.bashrc`?
    

### Lesson

A configuration file is just a file until the shell executes its commands.

---

## Experiment 2.3: Compare interactive and non-interactive shells

Add the marker again:

```bash
echo 'echo "[.bashrc loaded]"' >> ~/.bashrc
```

Run:

```bash
bash
```

Exit:

```bash
exit
```

Now run:

```bash
bash -c 'echo "[non-interactive shell ran]"'
```

Remove the marker:

```bash
sed -i '/\[.bashrc loaded\]/d' ~/.bashrc
```

### Questions

1. Did the marker appear in the interactive shell?
    
2. Did it appear in the `bash -c` shell?
    
3. What does this tell you about interactive versus non-interactive startup behavior?
    

### Lesson

Not every Bash invocation reads the same startup files.

---

## Experiment 2.4: Use tracing to observe startup behavior

Run:

```bash
bash -x
```

The shell prints commands as it executes them.

Exit:

```bash
exit
```

To focus on `.bashrc`, run:

```bash
bash -x 2>&1 | less
```

Exit the nested shell, then quit `less` with:

```text
q
```

### Questions

1. Can you see commands from startup files being executed?
    
2. Does the output make `.bashrc` look more like executable configuration than static documentation?
    

### Lesson

Shell startup files contain commands. They are not special database files.

---

## Experiment 2.5: Add a persistent alias

Run:

```bash
echo "alias c='clear'" >> ~/.bashrc
```

Try the alias immediately:

```bash
c
```

It should fail:

```text
bash: c: command not found
```

Now activate the edited file:

```bash
source ~/.bashrc
```

Try again:

```bash
c
```

Confirm the alias:

```bash
type c
```

Remove it afterward:

```bash
sed -i "/alias c='clear'/d" ~/.bashrc
source ~/.bashrc
```

### Questions

1. Why did the alias fail before `source ~/.bashrc`?
    
2. What does `source` do?
    
3. Why does opening a new terminal also activate the change?
    

### Lesson

Editing a startup file affects future shells. `source` applies the changes to the current shell.

---

## Experiment 2.6: Break `.bashrc` safely and recover

First create another backup:

```bash
cp ~/.bashrc ~/.bashrc.before-break-test
```

Add a harmless invalid command:

```bash
echo 'this-command-does-not-exist' >> ~/.bashrc
```

Start a nested shell:

```bash
bash
```

You should see an error.

Exit:

```bash
exit
```

Restore the file:

```bash
cp ~/.bashrc.before-break-test ~/.bashrc
```

### Question

Why is backing up a startup file before editing it a good habit?

### Lesson

Startup-file mistakes can affect every new shell. Small edits and backups matter.

---

# Section 3: Modifying the environment

## Experiment 3.1: Temporary variable assignment

Run:

```bash
FAVORITE_EDITOR=nano
echo "$FAVORITE_EDITOR"
```

Exit the current SSH session:

```bash
exit
```

Reconnect to `playground01`, then run:

```bash
echo "$FAVORITE_EDITOR"
```

### Expected result

The variable is gone.

### Lesson

A variable created at the prompt exists only in that shell session unless startup files recreate it.

---

## Experiment 3.2: Temporary environment variable for one command

Run:

```bash
LANG=C date
date
```

Also try:

```bash
MESSAGE=hello bash -c 'echo "$MESSAGE"'
```

Then inspect the current shell:

```bash
echo "$MESSAGE"
```

### Questions

1. Did `MESSAGE` exist inside the child command?
    
2. Did it remain defined in the parent shell?
    
3. Why might assigning a variable for one command be useful?
    

### Lesson

You can modify the environment of a single command without permanently changing your shell.

---

## Experiment 3.3: Add a directory to `PATH` temporarily

Create an executable script:

```bash
cat > ~/ch11-experiments/bin/hello-playground <<'EOF'
#!/usr/bin/env bash
echo "hello from $HOME/ch11-experiments/bin"
EOF
```

Make it executable:

```bash
chmod 755 ~/ch11-experiments/bin/hello-playground
```

Try to run it by name:

```bash
hello-playground
```

It should probably fail.

Run it with its full path:

```bash
~/ch11-experiments/bin/hello-playground
```

Now prepend the directory to `PATH`:

```bash
export PATH="$HOME/ch11-experiments/bin:$PATH"
```

Try again:

```bash
hello-playground
```

### Questions

1. Why did the command fail before modifying `PATH`?
    
2. Why did the full pathname work?
    
3. Why does Bash find the script after the `PATH` change?
    

### Lesson

`PATH` controls command lookup.

---

## Experiment 3.4: Prove that `PATH` order matters

Create two scripts with the same name:

```bash
mkdir -p ~/ch11-experiments/bin-first
mkdir -p ~/ch11-experiments/bin-second
```

Create the first script:

```bash
cat > ~/ch11-experiments/bin-first/greeting <<'EOF'
#!/usr/bin/env bash
echo "first directory"
EOF
```

Create the second script:

```bash
cat > ~/ch11-experiments/bin-second/greeting <<'EOF'
#!/usr/bin/env bash
echo "second directory"
EOF
```

Make both executable:

```bash
chmod 755 ~/ch11-experiments/bin-first/greeting
chmod 755 ~/ch11-experiments/bin-second/greeting
```

Set `PATH`:

```bash
export PATH="$HOME/ch11-experiments/bin-first:$HOME/ch11-experiments/bin-second:$PATH"
```

Run:

```bash
greeting
type -a greeting
```

Reverse the order:

```bash
export PATH="$HOME/ch11-experiments/bin-second:$HOME/ch11-experiments/bin-first:$PATH"
```

Run:

```bash
greeting
type -a greeting
```

### Question

Why did the output change?

### Lesson

Bash normally uses the first matching executable in `PATH`. Directory order is significant.

---

## Experiment 3.5: Make a `PATH` change persistent

Add a clearly labeled line:

```bash
echo 'export PATH="$HOME/ch11-experiments/bin:$PATH" # ch11 experiment' >> ~/.bashrc
```

Start a new shell:

```bash
bash
```

Run:

```bash
hello-playground
```

Exit:

```bash
exit
```

Remove the experimental line:

```bash
sed -i '/# ch11 experiment/d' ~/.bashrc
```

### Questions

1. Why did the script work in the new shell?
    
2. Why is a comment useful in `.bashrc`?
    
3. Why should experimental configuration lines be removed afterward?
    

### Lesson

Persistent environment changes belong in an appropriate startup file.

---

## Experiment 3.6: Compare `export NAME=value` and `NAME=value`

Run:

```bash
CITY=StLouis
bash -c 'echo "child sees CITY=$CITY"'
```

Now run:

```bash
export CITY=StLouis
bash -c 'echo "child sees CITY=$CITY"'
```

Remove the variable:

```bash
unset CITY
echo "$CITY"
```

### Questions

1. What changed after `export`?
    
2. What does `unset` do?
    
3. Why should variables be exported only when child processes need them?
    

### Lesson

Do not export variables automatically without understanding why. Exporting affects descendant processes.

---

## Experiment 3.7: Inspect before and after modifying the environment

Capture the environment:

```bash
printenv | sort > ~/ch11-experiments/env-before.txt
```

Create and export a variable:

```bash
export CH11_TEST_VARIABLE=visible
```

Capture again:

```bash
printenv | sort > ~/ch11-experiments/env-after.txt
```

Compare:

```bash
diff -u ~/ch11-experiments/env-before.txt ~/ch11-experiments/env-after.txt
```

Clean up:

```bash
unset CH11_TEST_VARIABLE
```

### Question

What exact line was added?

### Lesson

Use tools to verify changes instead of relying on memory.

---

# Cleanup

Restore the original `.bashrc` after the experiments:

```bash
cp ~/.bashrc.backup-ch11 ~/.bashrc
source ~/.bashrc
```

Remove the practice files when finished:

```bash
rm -rf ~/ch11-experiments
```

# What he should learn from each section

|Section|Core lesson|
|---|---|
|What is stored in the environment?|The shell stores variables, exported environment variables, functions, aliases, and other state.|
|How is the environment established?|Startup files execute commands when certain kinds of shells begin.|
|Modifying the environment|A change may be temporary, exported to children, applied to one command, or made persistent through a startup file.|

The most important experiments are 1.5, 2.2, 2.5, 3.3, and 3.4. Those reveal the behavior that beginners commonly misunderstand.