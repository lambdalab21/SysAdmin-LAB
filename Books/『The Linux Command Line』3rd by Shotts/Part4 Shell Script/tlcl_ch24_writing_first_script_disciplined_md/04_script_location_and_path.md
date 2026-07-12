#Book/The-Linux-Command-Line #Author/Shotts 
#shell-script
# Author/Shotts Chapter 24: Writing Your First Script

Use this guide with William Author/Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

This chapter starts Part IV of the book: shell scripting. The goal is not to type a few scripts and feel finished. The goal is to learn how a command sequence becomes a repeatable tool.

Core discipline:

```text
Do not type first.
Think first.
Write the English purpose first.
Predict what the script should do.
Run it safely.
Verify the result.
Explain every line.
```

One-time lab setup:

```bash
mkdir -p ~/tlcl-ch24-lab/{bin,scripts,tmp,output}
cd ~/tlcl-ch24-lab
```

Use this answer template throughout the chapter:

```text
Purpose of this script:
Input it uses:
Output it produces:
Commands inside it:
How I will run it:
How I will verify it worked:
What could go wrong:
```

---
# Session 4: Script Location, Good Locations, and PATH

## Read before exercise

Read these Chapter 24 sections:

```text
Script File Location
Good Locations for Scripts
```

## What he should gain from this reading

He should gain this idea:

```text
The shell only runs a command by name if it can find it.
PATH tells the shell where to look.
```

---

# Feynman analogy

Imagine the shell has a list of drawers where it looks for tools.

That list is `PATH`.

If your script is not in one of those drawers, typing only the script name will not find it.

```text
./system-info = use the file right here
system-info = search PATH for a command with that name
```

---

# After-reading questions

Answer before typing:

1. What is `PATH`?
2. Why does `./system-info` work when `system-info` may not?
3. Why is putting personal scripts in `~/bin` or `~/.local/bin` common?
4. Why should he avoid scattering scripts randomly?
5. What command shows the current `PATH`?

---

# Exercise 1: Inspect PATH

Run:

```bash
echo "$PATH"
printf '%s
' ${PATH//:/
}
```

Answer:

```text
Which directories are in PATH?
Is ~/bin present?
Is ~/.local/bin present?
```

Now use documentation/help:

```bash
help export
man bash
```

In `man bash`, search for `PATH`.

Do not try to understand the whole Bash manual. Find the relevant paragraph.

---

# Exercise 2: Create a personal bin directory

Run:

```bash
mkdir -p ~/bin
cp ~/tlcl-ch24-lab/scripts/system-info ~/bin/system-info
ls -l ~/bin/system-info
```

Try:

```bash
system-info
```

If it fails, run:

```bash
export PATH="$HOME/bin:$PATH"
system-info
```

Answer:

```text
Did it work before adding ~/bin to PATH?
Did it work after?
Why?
```

---

# Exercise 3: Confirm which script is being run

Run:

```bash
type system-info
command -v system-info
```

Answer:

```text
What exact path is being used?
Why should I check this before trusting a command name?
```

---

# Exercise 4: Do not edit shell startup files yet

This chapter may discuss good places for scripts. It is tempting to immediately edit `.bashrc`.

For now, do not make permanent PATH changes unless instructed by a teacher.

Instead, write what you would add later:

```bash
export PATH="$HOME/bin:$PATH"
```

Answer:

```text
Which file might this go in later?
Why is temporary export safer while learning?
```

---

# Explain-back

Explain:

```bash
export PATH="$HOME/bin:$PATH"
```

Use:

```text
PATH is...
$HOME/bin is...
:$PATH keeps...
export makes...
```

---

# Read after exercise

Reread “Script File Location” and “Good Locations for Scripts.”

This time, answer:

```text
What is the difference between a practice script and a personal tool I plan to keep?
```

---

# Session 4 checkpoint

He is ready for Session 5 only if he can answer:

1. What is PATH?
2. Why does `./script` not use PATH?
3. What does `type command` tell you?
4. Why is `~/bin` a reasonable personal script location?
5. Why should permanent PATH changes be made carefully?
