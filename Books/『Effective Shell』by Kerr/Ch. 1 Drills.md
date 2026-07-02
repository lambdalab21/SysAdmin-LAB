A cleaner phrasing is: “Please create drills for Chapter 1 of _Effective Shell_ by Kerr. Emphasize shortcuts and tricks that are not used often so that he will not forget them.”

Chapter 1, **“Flying on the Command Line,”** covers cursor movement, deleting text efficiently, undoing edits, searching command history, editing a command inside a text editor, clearing the screen, inspecting history, rerunning a numbered history entry, listing key bindings, and transposing text. Kerr specifically recommends practicing the techniques while reading because they are difficult to learn passively. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

Use Bash on `playground01`. Each set takes about 2–5 minutes.

# One-time setup

```bash
mkdir -p ~/effective-shell-ch01
cd ~/effective-shell-ch01

echo "$SHELL"
export EDITOR=vim
```

Do not press `Enter` when a drill says **edit only**. Cancel the line with:

```text
Ctrl-c
```

---

# Drill Set A: Cursor movement

These shortcuts should become automatic, even though they are not obscure.

Type this line without executing it:

```bash
echo alpha beta gamma delta epsilon >> note.txt
```

Perform:

```text
Ctrl-a       jump to the beginning
Ctrl-e       jump to the end
Alt-b        move backward one word
Alt-b        move backward one more word
Alt-f        move forward one word
Ctrl-c       cancel
```

Repeat twice.

Kerr emphasizes `Ctrl-a`, `Ctrl-e`, `Alt-b`, and `Alt-f` because they are faster than repeated arrow-key presses on long commands. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set B: Delete backward versus forward

This distinction is easy to forget.

## Delete backward with `Ctrl-w`

Type:

```bash
echo alpha beta gamma delta
```

Place the cursor at the end. Press:

```text
Ctrl-w
Ctrl-w
Ctrl-c
```

Observe which words disappear.

## Delete forward with `Alt-d`

Type:

```bash
echo alpha beta gamma delta
```

Press:

```text
Ctrl-a
Alt-f
Alt-d
Alt-d
Ctrl-c
```

Observe which words disappear.

## Repeat with punctuation

Type:

```bash
echo report-old.txt
```

Move around the filename and compare:

```text
Ctrl-w
```

with:

```text
Alt-d
```

Kerr points out a subtle difference: `Ctrl-w` deletes backward using whitespace boundaries, whereas `Alt-d` deletes forward using word boundaries based on alphanumeric characters. Punctuation can therefore produce different behavior. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set C: Delete part of a line

Type:

```bash
echo KEEP-LEFT remove everything on the right
```

Move the cursor immediately after `KEEP-LEFT`, then press:

```text
Ctrl-k
Ctrl-c
```

Type:

```bash
remove everything on the left echo KEEP-RIGHT
```

Move the cursor to the beginning of `echo`, then press:

```text
Ctrl-u
Ctrl-c
```

Say aloud:

```text
Ctrl-u = delete from cursor to beginning
Ctrl-k = delete from cursor to end
```

Repeat twice.

---

# Drill Set D: Undo an edit

`Ctrl-_` is useful but easy to forget.

Type:

```bash
echo alpha beta gamma
```

Then:

```text
Ctrl-w
Ctrl-w
Ctrl-_
Ctrl-_
Ctrl-c
```

Observe whether the deleted words return.

Repeat with:

```bash
echo one two three four five
```

Kerr includes `Ctrl-_` as Bash’s undo shortcut. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set E: Reverse history search

Seed the history:

```bash
touch file1 file2 file3
touch file4 file5 file6
touch file7 file8 file9
mkdir -p alpha beta gamma
echo "search-practice"
```

Search backward:

```text
Ctrl-r
```

Type:

```text
touch
```

Press repeatedly:

```text
Ctrl-r
Ctrl-r
Ctrl-r
```

Cancel the search without executing anything:

```text
Ctrl-g
```

Repeat with:

```text
mkdir
```

Then retrieve one command, edit it, and press `Enter`.

Say aloud:

```text
Ctrl-r = search backward
Ctrl-g = cancel the search
```

Kerr shows repeated `Ctrl-r` presses to move backward through matching history entries and `Ctrl-g` to abandon the search. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set F: Forward history search

`Ctrl-s` is less commonly practiced because terminal flow control may intercept it.

Temporarily disable software flow control in the current shell:

```bash
stty -ixon
```

Seed and search:

```bash
echo apple
echo banana
echo apricot
echo blueberry
```

Press:

```text
Ctrl-r
```

Type:

```text
echo
```

Move backward through matches:

```text
Ctrl-r
Ctrl-r
Ctrl-r
```

Then move forward:

```text
Ctrl-s
Ctrl-s
```

Cancel:

```text
Ctrl-g
```

Restore the terminal setting afterward:

```bash
stty ixon
```

Bash binds `Ctrl-r` to incremental reverse history search and `Ctrl-s` to incremental forward history search. On terminals using XON/XOFF software flow control, `Ctrl-s` may instead pause terminal output until `Ctrl-q` is pressed; `stty -ixon` disables that interception for the session. ([GNU](https://www.gnu.org/software/bash/manual/html_node/Commands-For-History.html?utm_source=chatgpt.com "Commands For History (Bash Reference Manual)"))

---

# Drill Set G: Edit a command inside Vim

This is one of the most useful tricks in the chapter for long commands.

Begin typing, but do not press `Enter`:

```bash
find /usr/bin -type f -name '*.sh' | sort | head
```

Press:

```text
Ctrl-x
Ctrl-e
```

The command should open in Vim.

Inside Vim, change:

```text
head
```

to:

```text
head -n 5
```

Save and exit:

```vim
:wq
```

The edited command should execute.

Repeat with:

```bash
printf '%s\n' alpha beta gamma delta epsilon | sort
```

Open it with:

```text
Ctrl-x
Ctrl-e
```

Change:

```text
sort
```

to:

```text
sort -r
```

Save and exit.

Kerr introduces `Ctrl-x Ctrl-e` for editing the current command in the shell’s configured terminal editor before execution. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set H: Clear the screen without losing the current command

Create noisy output:

```bash
seq 1 100
```

Begin typing this command without executing it:

```bash
echo "this unfinished command must survive"
```

Press:

```text
Ctrl-l
```

The screen clears, but the unfinished command should remain at the prompt.

Cancel it:

```text
Ctrl-c
```

Repeat once.

Kerr notes that `Ctrl-l` clears visible terminal clutter without deleting the unexecuted command line. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set I: Inspect history and rerun a numbered entry

Run harmless commands:

```bash
echo first-history-test
pwd
echo second-history-test
```

Inspect recent history:

```bash
history | tail
```

Locate the number beside:

```text
echo first-history-test
```

Rerun it by typing:

```bash
!NUMBER
```

For example:

```bash
!417
```

Inspect the history-file location:

```bash
echo "$HISTFILE"
```

## Safety rule

Use `!NUMBER` only for harmless practice commands unless the exact expanded command is known. History expansion executes the selected entry immediately.

Kerr introduces `history`, `!number`, and `$HISTFILE` as ways to inspect and reuse shell history. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set J: Inspect Bash keyboard bindings

On Bash, run:

```bash
bind -p | less
```

Search inside `less`:

```text
/beginning-of-line
```

Then search:

```text
/end-of-line
```

Then:

```text
/kill-line
```

Then:

```text
/reverse-search-history
```

Exit:

```text
q
```

For a short filtered view:

```bash
bind -p | grep -E 'beginning-of-line|end-of-line|kill-line|unix-line-discard|reverse-search-history|transpose'
```

Kerr mentions `bindkey` and the alternative `bind -p`; for Bash on `playground01`, use `bind -p`. Bash uses Readline for command-line editing and allows key bindings to be inspected or changed. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set K: Transpose characters

Type this misspelled command without executing it:

```bash
ehco hello
```

Place the cursor after the `h`, then press:

```text
Ctrl-t
```

The command should become:

```bash
echo hello
```

Execute it.

Repeat with:

```text
pws
sl -l
cta /etc/hostname
```

Correct each line before executing it.

Say aloud:

```text
Ctrl-t = swap adjacent characters
```

Kerr includes `Ctrl-t` for transposing characters. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Drill Set L: Transpose words

Type:

```bash
echo second first
```

Place the cursor at the end and press:

```text
Alt-t
```

The command should become:

```bash
echo first second
```

Execute it.

Repeat with:

```bash
cp destination.txt source.txt
```

Do **not** execute this line. Correct it with:

```text
Alt-t
```

Then cancel:

```text
Ctrl-c
```

Say aloud:

```text
Alt-t = swap adjacent words
```

Kerr includes `Alt-t` for transposing words. ([ノースターチプレス](https://nostarch.com/download/EffectiveShell_chapter1.pdf "EffectiveShell_txt_1p.indd"))

---

# Two-minute uncommon-shortcut drill

Use this when there is little time.

Type:

```bash
echo alpha beta gamma delta epsilon
```

Then perform:

```text
Ctrl-w
Alt-d
Ctrl-_
Ctrl-k
Ctrl-_
Ctrl-u
Ctrl-_
Ctrl-c
```

Seed history:

```bash
echo uncommon-shortcut-practice
```

Then perform:

```text
Ctrl-r
```

Type:

```text
uncommon
```

Cancel:

```text
Ctrl-g
```

Finally, type:

```bash
echo second first
```

Correct it with:

```text
Alt-t
```

Execute it.

---

# Five-minute daily rotation

|Day|Drill sets|
|---|---|
|Monday|B, C, D|
|Tuesday|E, F|
|Wednesday|G|
|Thursday|H, I|
|Friday|J, K, L|
|Saturday|Two-minute uncommon-shortcut drill|
|Sunday|Repeat the weakest set|

# Shortcuts to retain

## Use constantly

```text
Ctrl-a       beginning of line
Ctrl-e       end of line
Alt-b        backward one word
Alt-f        forward one word
Ctrl-r       search backward through history
Ctrl-l       clear screen
```

## Practice deliberately because they are easy to forget

```text
Ctrl-w       delete backward
Alt-d        delete forward
Ctrl-u       delete to beginning
Ctrl-k       delete to end
Ctrl-_       undo
Ctrl-g       cancel history search
Ctrl-s       search forward after stty -ixon
Ctrl-x Ctrl-e
             edit command in terminal editor
Ctrl-t       transpose characters
Alt-t        transpose words
!NUMBER      execute numbered history entry
bind -p      inspect Bash key bindings
echo "$HISTFILE"
             locate Bash history file
```

The most important habit is not memorizing the list. It is noticing repeated arrow-key presses, repeated backspaces, and retyping of long commands, then replacing those inefficient movements with the appropriate shortcut.