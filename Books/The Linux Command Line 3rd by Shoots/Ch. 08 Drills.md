#Book/The-Linux-Command-Line  #Author/Shotts 
#key-binding #shortcut 

Chapter 8, **“Advanced Keyboard Tricks,”** is about reducing unnecessary typing by using Bash’s Readline editing features, tab completion, command history, and the `script` command. Author/Shotts explicitly says that the student does not need to learn every shortcut immediately. The goal is to select the useful ones and gradually turn them into habits. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

Do not treat this as a memorization assignment. A student may remember a shortcut for a quiz but continue pressing arrow keys and retyping commands during real work. The drills below require him to use the shortcuts repeatedly.

# Chapter 8 Lab: Bash Keyboard-Shortcut Drills

## Learning objectives

By the end of the lab, the student should be able to:

1. Edit a long command without repeatedly pressing arrow keys.
    
2. delete and restore text without using the mouse;
    
3. use `Tab` completion instead of typing complete paths;
    
4. retrieve a previous command with `Ctrl-r`;
    
5. search history with `history` and `grep`;
    
6. record a terminal session with `script`.
    

Complete the drills in Bash on `app01` or another Linux machine. Do not use the mouse while completing them.

---

# Part 0: Prepare a safe practice area

Create a directory containing deliberately similar file and directory names:

```bash
mkdir -p ~/tlcl-ch08/{documents,downloads,deploy,development,logs}
touch ~/tlcl-ch08/documents/{report.txt,report-old.txt,readme.txt}
touch ~/tlcl-ch08/logs/{app.log,app-error.log,access.log}
cd ~/tlcl-ch08
```

Confirm the result:

```bash
find . -maxdepth 2 -type f
```

All editing drills use harmless commands such as `echo`, `ls`, and `find`. When instructed **not** to press `Enter`, leave the edited command on the prompt and cancel it with:

```text
Ctrl-c
```

---

# Part 1: Core shortcuts

These are the shortcuts he should begin using in ordinary terminal work immediately.

|Shortcut|Purpose|
|---|---|
|`Ctrl-a`|Move to the beginning of the line|
|`Ctrl-e`|Move to the end of the line|
|`Alt-b`|Move backward one word|
|`Alt-f`|Move forward one word|
|`Ctrl-u`|Cut from the cursor to the beginning of the line|
|`Ctrl-k`|Cut from the cursor to the end of the line|
|`Alt-d`|Cut from the cursor to the end of the word|
|`Alt-Backspace`|Cut the previous word|
|`Ctrl-y`|Paste previously cut text|
|`Ctrl-l`|Clear the screen|
|`Tab`|Complete a command, filename, or path|
|`Ctrl-r`|Search command history backward|

Bash uses the Readline library for command-line editing. In Readline terminology, cutting text is called **killing**, pasting is called **yanking**, and the temporary storage area is the **kill ring**. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

Some terminal emulators or desktop environments intercept `Alt` combinations. When an `Alt` shortcut does not work, press and release `Esc`, then press the second key. For example, use `Esc`, then `b` as an alternative to `Alt-b`. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

---

# Part 2: Cursor-movement drills

## Drill 2.1: Jump to the beginning and end

Type this command, but **do not press `Enter`**:

```bash
echo alpha beta gamma delta epsilon
```

Complete the following actions:

1. Press `Ctrl-a`. The cursor should jump to the beginning.
    
2. Add `START:` before `echo`.
    
3. Press `Ctrl-e`. The cursor should jump to the end.
    
4. Add `:END`.
    
5. Press `Ctrl-c` to cancel the line.
    

Repeat five times.

### Rule

Do not use `Home`, `End`, the mouse, or repeated arrow-key presses.

---

## Drill 2.2: Move one word at a time

Type:

```bash
echo one two three four five six seven
```

Do not press `Enter`.

1. Press `Alt-b` three times.
    
2. Type `INSERTED` .
    
3. Press `Alt-f` twice.
    
4. Type `HERE`.
    
5. Cancel with `Ctrl-c`.
    

Repeat five times.

### Lesson

`Alt-b` and `Alt-f` are more efficient than holding down the left or right arrow key when editing long commands. Author/Shotts lists these as word-level cursor-movement commands. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

---

## Drill 2.3: Repair a realistic command

Type this incorrect command:

```bash
find ~/tlcl-ch08 -type f -name "*.txt" -maxdepth 2
```

Without clearing and retyping the entire line, change it to:

```bash
find ~/tlcl-ch08 -maxdepth 2 -type f -name "*.log"
```

Execute the corrected command.

### Restrictions

Use:

- `Ctrl-a` or `Ctrl-e`;
    
- `Alt-b` or `Alt-f`;
    
- `Alt-Backspace`;
    
- `Alt-d`.
    

Do not use the mouse.

### Lesson

A terminal user should not automatically erase and retype a long command merely because one argument is wrong.

---

# Part 3: Killing and yanking drills

## Drill 3.1: Remove the beginning of a line with `Ctrl-u`

Type:

```bash
this text should disappear echo keep-this-part
```

Do not press `Enter`.

Move the cursor to the beginning of `echo`, then press:

```text
Ctrl-u
```

The line should become:

```bash
echo keep-this-part
```

Press `Enter`.

Repeat three times.

---

## Drill 3.2: Remove the end of a line with `Ctrl-k`

Type:

```bash
echo keep-this-part remove this entire ending
```

Move the cursor to the space immediately after `keep-this-part`. Press:

```text
Ctrl-k
```

Execute the remaining command.

Repeat three times.

---

## Drill 3.3: Remove one word with `Alt-d`

Type:

```bash
echo alpha REMOVE-ME beta gamma
```

Move to the beginning of `REMOVE-ME`, then press:

```text
Alt-d
```

Execute the corrected command.

Repeat three times.

---

## Drill 3.4: Remove the previous word with `Alt-Backspace`

Type:

```bash
echo alpha beta MISTAKE gamma
```

Move the cursor to the space after `MISTAKE`, then press:

```text
Alt-Backspace
```

Execute the corrected command.

Repeat three times.

---

## Drill 3.5: Cut and paste with `Ctrl-u` and `Ctrl-y`

Type:

```bash
echo first second third
```

Do not press `Enter`.

1. Press `Ctrl-a`.
    
2. Move to the beginning of `third`.
    
3. Press `Ctrl-u`.
    
4. The beginning of the line disappears.
    
5. Press `Ctrl-e`.
    
6. Press `Ctrl-y`.
    

Observe where the cut text is restored. Cancel with `Ctrl-c`.

Repeat three times.

### Lesson

The value is not merely deleting text. The cut text can be inserted somewhere else with `Ctrl-y`. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

---

# Part 4: Minor editing shortcuts

These are useful, but less important than the Part 1 shortcuts.

|Shortcut|Purpose|
|---|---|
|`Ctrl-d`|Delete the character under the cursor|
|`Ctrl-t`|Swap two adjacent characters|
|`Alt-t`|Swap adjacent words|
|`Alt-l`|Convert text to lowercase|
|`Alt-u`|Convert text to uppercase|

Author/Shotts includes these as text-editing commands, but they should not receive equal practice time. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

## Drill 4.1: Fix a transposed character

Type this misspelled command but do not execute it:

```bash
ehco hello
```

Place the cursor after the `h` and press:

```text
Ctrl-t
```

The command should become:

```bash
echo hello
```

Execute it.

Repeat with:

```bash
sl -l
cta /etc/hostname
pws
```

Do not execute a command until it is corrected.

---

## Drill 4.2: Swap words

Type:

```bash
echo second first
```

Use `Alt-t` to make it:

```bash
echo first second
```

Execute it.

---

# Part 5: Tab-completion drills

Tab completion works with paths, commands, variables, usernames, and certain hostnames. When the entered text matches multiple possible completions, pressing `Tab` once may do nothing. Pressing `Tab` twice displays the possible matches. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

## Drill 5.1: Complete a unique directory name

Type only:

```bash
cd ~/tlcl-ch08/do
```

Press `Tab`.

It should expand to:

```bash
cd ~/tlcl-ch08/documents/
```

Press `Enter`, then return:

```bash
cd ~/tlcl-ch08
```

---

## Drill 5.2: Discover ambiguous matches

Type:

```bash
cd ~/tlcl-ch08/d
```

Press `Tab` twice.

The shell should show several possibilities, including:

```text
deploy/
development/
documents/
downloads/
```

Add enough letters to select one directory and press `Tab` again.

### Lesson

Do not type complete directory names manually when Bash can finish them safely.

---

## Drill 5.3: Complete filenames

Type as little as possible while using `Tab` to execute:

```bash
cat ~/tlcl-ch08/documents/readme.txt
```

Then use completion to execute:

```bash
ls -l ~/tlcl-ch08/logs/app-error.log
```

---

## Drill 5.4: Complete commands

Type:

```bash
hist
```

Press `Tab`.

It should complete to:

```bash
history
```

Execute it.

Try the same technique with:

```bash
cle
```

and:

```bash
fin
```

When more than one completion is possible, press `Tab` twice and inspect the choices.

---

## Habit rule

During later labs, manually typing a long pathname such as:

```bash
/var/www/site1/public/
```

should be treated as a mistake when tab completion could have been used.

---

# Part 6: History drills

Bash stores previous commands in its history list. Author/Shotts emphasizes that history becomes especially useful when combined with command-line editing. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

## Drill 6.1: Seed the history list

Run these commands:

```bash
echo "alpha"
echo "beta"
ls -l ~/tlcl-ch08/documents
find ~/tlcl-ch08 -type f
ls -l ~/tlcl-ch08/logs
grep root /etc/passwd
date
```

---

## Drill 6.2: Search history with `grep`

Search for previous `ls` commands:

```bash
history | grep 'ls -l'
```

Search for the previous `find` command:

```bash
history | grep find
```

### Lesson

This is useful when the relevant command was run too long ago for repeated Up-arrow presses to be practical.

---

## Drill 6.3: Reverse-search with `Ctrl-r`

1. Press `Ctrl-r`.
    
2. Type:
    

```text
find
```

3. Bash should display the previously executed `find` command.
    
4. Press `Ctrl-j` to copy the command to the current prompt without executing it.
    
5. Edit the command so that it searches only for `.txt` files:
    

```bash
find ~/tlcl-ch08 -type f -name '*.txt'
```

6. Press `Enter`.
    

Repeat the drill with:

```text
passwd
```

to retrieve:

```bash
grep root /etc/passwd
```

### Important habit

Prefer `Ctrl-r` over repeatedly pressing the Up arrow when looking for a specific older command.

---

## Drill 6.4: Cycle through matching history entries

Run:

```bash
echo apple
echo banana
echo apricot
echo blueberry
echo avocado
```

Then:

1. press `Ctrl-r`;
    
2. type `echo a`;
    
3. press `Ctrl-r` repeatedly.
    

Observe that Bash cycles backward through matching commands.

Cancel with:

```text
Ctrl-c
```

---

## Drill 6.5: Navigate history without arrow keys

Use:

|Shortcut|Purpose|
|---|---|
|`Ctrl-p`|Previous history entry|
|`Ctrl-n`|Next history entry|

Run several harmless commands, then navigate backward and forward through them without using the arrow keys.

These shortcuts perform the same operations as Up-arrow and Down-arrow. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

---

# Part 7: History-expansion experiments

History expansion is worth recognizing, but it should **not** become the student's default workflow. `Ctrl-r` followed by editing is generally easier to inspect and control.

Author/Shotts warns that some history expansions can execute an unintended previous command. He recommends appending `:p` to print an expansion before running it. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

## Drill 7.1: Repeat the last command

Run:

```bash
echo safe-command
```

Then run:

```bash
!!
```

Observe that the previous command runs again.

---

## Drill 7.2: Preview before executing

Run:

```bash
ls -l ~/tlcl-ch08/documents
```

Then type:

```bash
!ls:p
```

This prints the most recent command beginning with `ls` without immediately executing it.

Press Up-arrow to retrieve the printed command. Inspect it, then press `Enter`.

---

## Drill 7.3: Understand the danger

Do **not** run destructive commands for this experiment.

Explain why this command could be risky:

```bash
!rm
```

Expected answer:

> It executes the most recent history entry beginning with `rm`. The user may not remember exactly which files or directories that command removes.

Safer alternatives:

```bash
!rm:p
```

or:

```text
Ctrl-r
```

followed by inspection and editing.

---

# Part 8: Clear the screen

Run several commands until the terminal is cluttered:

```bash
find ~/tlcl-ch08
history | tail
ls -la
```

Clear the screen without typing `clear`:

```text
Ctrl-l
```

`Ctrl-l` performs the same basic function as the `clear` command. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

---

# Part 9: Record a terminal session with `script`

The `script` command records a terminal session to a file. Author/Shotts presents its basic syntax as `script [file]`. ([Linux Class](https://linuxclass.heinz.cmu.edu/doc/TLCL-25.12.pdf "The Linux Command Line, 7th Internet Edition"))

Start recording:

```bash
cd ~/tlcl-ch08
script shortcut-practice.txt
```

During the recorded session, run:

```bash
pwd
ls
find . -maxdepth 2 -type f
history | tail
```

End the recording:

```bash
exit
```

Inspect the file:

```bash
less shortcut-practice.txt
```

### Questions

1. What information did `script` record?
    
2. Does it capture only commands, or also output?
    
3. How is `script` different from `history`?
    
4. In what situations might a system administrator record a terminal session?
    

Expected core answer:

> `history` primarily stores entered commands. `script` records the visible terminal session, including command output. This can help document a procedure, preserve evidence from troubleshooting, or review work afterward.

---

# Part 10: Realistic editing challenge

Type the following incorrect command exactly as shown:

```bash
find ~/tlcl-ch08/documents -type f -name "*.log" -maxdepth 4
```

Change it into:

```bash
find ~/tlcl-ch08/logs -maxdepth 1 -type f -name "*.log"
```

Rules:

1. Do not retype the entire command.
    
2. Do not use the mouse.
    
3. Use word movement rather than repeated arrow-key presses.
    
4. Use at least one killing shortcut.
    
5. Use `Tab` completion while correcting the directory path.
    

Then retrieve the corrected command using `Ctrl-r`, modify it to search for `.txt` files in `documents`, and execute it again.

---

# Part 11: Timed daily drill

Complete this routine for five days. It should take about 10 minutes.

## Round A: movement — 2 minutes

Type a long `echo` command and practice:

```text
Ctrl-a
Ctrl-e
Alt-b
Alt-f
```

## Round B: killing and yanking — 2 minutes

Practice:

```text
Ctrl-u
Ctrl-k
Alt-d
Alt-Backspace
Ctrl-y
```

## Round C: completion — 2 minutes

Navigate through:

```bash
~/tlcl-ch08/documents
~/tlcl-ch08/downloads
~/tlcl-ch08/deploy
~/tlcl-ch08/development
```

Type as few characters as possible and rely on `Tab`.

## Round D: history — 2 minutes

Retrieve earlier commands with:

```text
Ctrl-r
```

Edit them before execution.

## Round E: realistic work — 2 minutes

SSH into a practice server, inspect directories, and use the shortcuts during normal terminal work.

---

# Mastery test

He passes the Chapter 8 shortcut lab when he can complete the following without looking at a cheat sheet:

1. move to the beginning and end of a command line;
    
2. move backward and forward by one word;
    
3. remove the beginning or end of a line;
    
4. remove one word;
    
5. paste previously removed text;
    
6. complete a pathname with `Tab`;
    
7. display multiple completion candidates;
    
8. retrieve an older command with `Ctrl-r`;
    
9. edit the retrieved command before executing it;
    
10. clear the screen with `Ctrl-l`;
    
11. record a terminal session with `script`.
    

He does **not** need to memorize every obscure shortcut in the chapter. The practical minimum is:

```text
Ctrl-a   Ctrl-e
Alt-b    Alt-f
Ctrl-u   Ctrl-k
Alt-d    Alt-Backspace
Ctrl-y   Ctrl-l
Tab      Ctrl-r
```

Those shortcuts should become automatic before adding less frequently used ones.