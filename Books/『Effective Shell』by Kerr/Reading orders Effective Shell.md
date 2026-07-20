#Book/Effective-Shell aAuthor/#Kerr


The books overlap substantially, but they should not be read in parallel chapter by chapter.

_The Linux Command Line_ is the foundation course. It introduces Linux, shell behavior, commands, permissions, processes, text-processing tools, and Bash scripting in a gradual sequence. _Effective Shell_ assumes some command-line familiarity and focuses on building an efficient workflow: pipelines, text manipulation, reliable scripts, shell configuration, Git-managed dotfiles, SSH, Vim, and `tmux`. No Starch Press explicitly describes _Effective Shell_ as a workflow blueprint rather than a tour of shell commands. ([ノースターチプレス](https://nostarch.com/effective-shell "Effective Shell | No Starch Press"))

Use Author/Shotts as the main text. Insert Kerr chapters when they reinforce a Author/Shotts topic.

# Chapter correspondence

## Part 1: Basic shell fluency

| _Effective Shell_ | Topic                      | Closest TLCL chapters            | Recommendation     |
| ----------------- | -------------------------- | -------------------------------- | ------------------ |
| ES 1              | Flying on the Command Line | TLCL 8: Advanced Keyboard Tricks | Read now           |
| ES 2              | Thinking in Pipelines      | TLCL 6: Redirection              | Read now           |
| ES 3              | Finding Files              | TLCL 17: Searching for Files     | Read after TLCL 17 |

Author/Shotts covers redirection in Chapter 6 and keyboard editing in Chapter 8. Kerr revisits those topics with a stronger focus on speed and practical habits. ([ノースターチプレス](https://nostarch.com/linux-command-line-3e "The Linux Command Line, 3rd Edition | No Starch Press"))

Your son has already completed the relevant Author/Shotts material, so reading Effective Shell Chapters 1 and 2 now is appropriate.

After ES Chapter 2, pause _Effective Shell_. Do not continue directly into Chapter 3 yet. Read ES Chapter 3 when he reaches TLCL Chapter 17.

---

## Part 2: Text processing

| _Effective Shell_ | Topic                                 | Closest TLCL chapters                      | Recommendation              |
| ----------------- | ------------------------------------- | ------------------------------------------ | --------------------------- |
| ES 4              | Regular Expression Essentials         | TLCL 19: Regular Expressions               | Read together               |
| ES 5              | Getting to Grips with `grep`          | TLCL 19–20                                 | Read immediately after ES 4 |
| ES 6              | Slicing and Dicing Text               | TLCL 20: Text Processing                   | Read together               |
| ES 7              | Advanced Text Manipulation with `sed` | TLCL 20: Text Processing                   | Read after TLCL 20          |
| ES 8              | Building Commands on the Fly          | TLCL 20 and selected material from TLCL 36 | Read after ES 7             |

Author/Shotts places regular expressions in Chapter 19 and text processing in Chapter 20. Kerr expands that material into five chapters, which is useful because text processing is central to Unix command-line work. ([ノースターチプレス](https://nostarch.com/linux-command-line-3e "The Linux Command Line, 3rd Edition | No Starch Press"))

This is one of the areas where Kerr should receive substantial attention.

A sensible sequence is:

```text
TLCL 19: Regular Expressions
→ ES 4: Regular Expression Essentials
→ ES 5: grep
→ TLCL 20: Text Processing
→ ES 6: Slicing and Dicing Text
→ ES 7: sed
→ ES 8: Building Commands on the Fly
```

Do not rush through this block. It teaches the reusable Unix habit:

```text
produce text
→ filter text
→ transform text
→ pass the result to another command
```

---

## Part 3: Shell scripting

|_Effective Shell_|Topic|Closest TLCL chapters|Recommendation|
|---|---|---|---|
|ES 9|Shell Script Fundamentals|TLCL 24–26|Read after TLCL 26|
|ES 10|Variables|TLCL 7, 25, and 34|Read with ES 9|
|ES 11|Conditional Logic|TLCL 27 and 31|Read after TLCL 27|
|ES 12|Loops with Files and Folders|TLCL 29 and 33|Read after TLCL 33|
|ES 13|Functions, Parameters, and Error Handling|TLCL 30 and 32|Read after TLCL 32|
|ES 14|Useful Patterns for Shell Scripts|TLCL 30 and 36|Read after TLCL 34|

Author/Shotts devotes Chapters 24–36 to shell scripts, progressing from a first script through project structure, conditions, loops, troubleshooting, positional parameters, strings, arrays, and advanced features. ([ノースターチプレス](https://nostarch.com/linux-command-line-3e "The Linux Command Line, 3rd Edition | No Starch Press"))

Kerr compresses the same territory into Chapters 9–14 and focuses more on practical patterns. ([ノースターチプレス](https://nostarch.com/effective-shell "Effective Shell | No Starch Press"))

Do **not** read both scripting sections independently from start to finish. Use Author/Shotts first because his progression is slower and better for first exposure. Then use Kerr as reinforcement.

Recommended sequence:

```text
TLCL 24–26
→ ES 9–10

TLCL 27
→ ES 11

TLCL 28–33
→ ES 12–13

TLCL 34
→ ES 14

TLCL 35–36
→ revisit selected ES 14 examples
```

This prevents redundant reading while still giving him a second explanation of the important material.

---

## Part 4: Shell configuration and personal toolkit

|_Effective Shell_|Topic|Closest TLCL chapters|Recommendation|
|---|---|---|---|
|ES 15|Configuring Your Shell|TLCL 11: The Environment|Read after TLCL 11|
|ES 16|Customizing Your Command Prompt|TLCL 13: Customizing the Prompt|Read selectively|
|ES 17|Managing Your Dot Files|No close TLCL equivalent|Read later|
|ES 18|Controlling Changes with Git|No close TLCL equivalent|Read after basic Git|
|ES 19|Remote Git Repositories and Sharing Dotfiles|No close TLCL equivalent|Read after ES 18|

Author/Shotts introduces the environment in Chapter 11 and prompt customization in Chapter 13. Kerr expands those topics and then adds a modern workflow: version-controlled dotfiles and remote Git repositories. ([ノースターチプレス](https://nostarch.com/effective-shell "Effective Shell | No Starch Press"))

For your son:

```text
TLCL 11
→ perform the environment-variable experiments
→ ES 15

TLCL 13
→ skim for understanding
→ ES 16 selectively
```

Your concern about decorative terminal customization still applies. He should understand `PS1`, startup files, and portable configuration. He does not need to spend time creating a colorful theme.

Delay ES 17–19 until he has basic Git experience. Those chapters become useful when he has meaningful configuration files to manage:

```text
.bashrc
.vimrc
.tmux.conf
.gitconfig
```

---

## Part 5: Advanced techniques

|_Effective Shell_|Topic|Closest TLCL chapters|Recommendation|
|---|---|---|---|
|ES 20|Shell Expansion|TLCL 7: Seeing the World as the Shell Sees It|Read after TLCL 7 or revisit later|
|ES 21|Alternatives to Shell Scripting|No direct equivalent|Read after the scripting block|
|ES 22|The Secure Shell|TLCL 16: Networking|Read after regular SSH use|
|ES 23|The Power of Terminal Editors|TLCL 12: Introduction to Vim|Skim or skip initially|
|ES 24|Mastering the Multiplexer|No close TLCL equivalent|Read when he starts sustained SSH work|

Kerr places shell expansion late in the book, while Author/Shotts introduces expansion early in TLCL Chapter 7. ([ノースターチプレス](https://nostarch.com/effective-shell "Effective Shell | No Starch Press"))

That different placement reflects the books’ purposes:

```text
Author/Shotts:
learn enough expansion early to understand shell behavior

Kerr:
revisit expansion later to use it deliberately and efficiently
```

ES Chapter 21 is particularly valuable. It teaches when shell scripting stops being the right tool. A competent sysadmin should know when to switch to Python, Go, or another language rather than forcing complex logic into Bash.

ES Chapter 22 should come after he has used SSH regularly with:

```text
app01
db01
playground01
```

ES Chapter 24 on `tmux` should come when he begins running commands through SSH often enough to benefit from persistent terminal sessions.

Since he already knows Vim, ES Chapter 23 should be skimmed for unfamiliar techniques rather than studied in full.

---

# Topics covered by Author/Shotts but not by Kerr

Kerr is not a replacement for Author/Shotts.

Continue reading TLCL normally for topics such as:

|TLCL chapter|Topic|
|---|---|
|9|Permissions|
|10|Processes|
|14|Package Management|
|15|Storage Media|
|16|Networking fundamentals|
|18|Archiving and Backup|
|21|Formatting Output|
|22|Printing|
|23|Compiling Programs|

Author/Shotts explicitly covers process control, package management, networking tools, filesystems, compilation, and core utilities as part of a broad Linux command-line education. ([ノースターチプレス](https://nostarch.com/linux-command-line-3e "The Linux Command Line, 3rd Edition | No Starch Press"))

Kerr focuses more narrowly on productive shell workflows for developers. ([ノースターチプレス](https://nostarch.com/effective-shell "Effective Shell | No Starch Press"))

---

# Recommended reading order from his current position

He is currently reading TLCL Chapter 10 and has started _Effective Shell_ Chapter 2.

Use this sequence:

```text
Now:
  finish ES 2
  finish TLCL 10

Next:
  TLCL 11
  → ES 15

Then:
  TLCL 12: skim because he already knows Vim
  TLCL 13: read briefly
  → ES 16 selectively

Continue TLCL:
  TLCL 14–16
  → networking experiments
  → Ward 9–10
  → Networking for System Administrators

Then:
  TLCL 17
  → ES 3

Then:
  TLCL 18

Then spend extra time here:
  TLCL 19
  → ES 4–5
  TLCL 20
  → ES 6–8

Continue:
  TLCL 21–23

Then scripting:
  TLCL 24–34 interleaved with ES 9–14

Later:
  ES 17–19
  ES 21–22
  ES 24

Skim:
  ES 23
```

# Compact map

|TLCL|Add the corresponding ES chapter|
|---|---|
|6: Redirection|ES 2|
|8: Keyboard Tricks|ES 1|
|11: Environment|ES 15|
|13: Prompt|ES 16 selectively|
|16: Networking|ES 22 later|
|17: Searching for Files|ES 3|
|19: Regular Expressions|ES 4–5|
|20: Text Processing|ES 6–8|
|24–26: Script Foundations|ES 9–10|
|27 and 31: Branching|ES 11|
|29 and 33: Loops|ES 12|
|30 and 32: Troubleshooting and Parameters|ES 13|
|34–36: More Advanced Scripting|ES 14 and ES 20|
|No direct TLCL equivalent|ES 17–19, 21, and 24|

# Bottom line

Use _Effective Shell_ selectively rather than sequentially for now.

The strongest pairings are:

```text
TLCL 6  + ES 2       pipelines
TLCL 17 + ES 3       finding files
TLCL 19 + ES 4–5     regex and grep
TLCL 20 + ES 6–8     text processing
TLCL 24–34 + ES 9–14 scripting
TLCL 11 + ES 15      shell configuration
```

The text-processing and scripting blocks deserve the most time. Those are the places where Kerr adds the most value beyond Author/Shotts.