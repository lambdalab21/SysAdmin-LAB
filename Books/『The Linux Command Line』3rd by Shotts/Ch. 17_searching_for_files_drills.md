# The Linux Command Line, Chapter 17 Drills: Searching for Files

A more natural phrasing: “Please create drills to practice Shotts’s Chapter 17.”

Chapter 17 of *The Linux Command Line* focuses on **searching for files**. The important commands and ideas are:

- `locate`
- `find`
- `updatedb`
- file tests such as `-name`, `-type`, `-size`, `-mtime`, `-user`, and `-perm`
- logical operators such as `-and`, `-or`, `-not`
- actions such as `-print`, `-delete`, `-ls`, `-exec`
- `xargs`
- `touch`
- `stat`

Use these drills on `playground01`. These are short drills, not long labs.

---

## One-time setup

Create a practice tree:

```bash
mkdir -p ~/tlcl-ch17-drills/{docs,logs,scripts,archive,tmp}
cd ~/tlcl-ch17-drills

touch docs/report.txt
touch docs/report-old.txt
touch docs/notes.md
touch docs/todo.txt

touch logs/app.log
touch logs/app-error.log
touch logs/access.log
touch logs/debug.tmp

touch scripts/deploy.sh
touch scripts/backup.sh
touch scripts/check-status.sh

touch archive/old-report.txt
touch archive/old-app.log

touch tmp/junk1.tmp
touch tmp/junk2.tmp
touch tmp/empty-file
```

Add content:

```bash
echo "Quarterly report" > docs/report.txt
echo "Old report" > docs/report-old.txt
echo "TODO: review permissions" > docs/todo.txt
echo "Application started" > logs/app.log
echo "ERROR: connection refused" > logs/app-error.log
echo "GET /index.html" > logs/access.log
echo '#!/usr/bin/env bash' > scripts/deploy.sh
echo 'echo deploy' >> scripts/deploy.sh
echo '#!/usr/bin/env bash' > scripts/backup.sh
echo 'echo backup' >> scripts/backup.sh
chmod 755 scripts/*.sh
```

Verify:

```bash
find ~/tlcl-ch17-drills -print
```

---

# Drill Set A: Basic `find`

Run:

```bash
cd ~/tlcl-ch17-drills
find .
find . -type f
find . -type d
```

Say aloud:

```text
-type f = ordinary files
-type d = directories
```

Repeat once without looking at notes.

---

# Drill Set B: Find by exact name

Run:

```bash
find . -name "report.txt"
find . -name "app.log"
find . -name "deploy.sh"
find . -name "missing.txt"
```

Expected result for `missing.txt`: no output.

Say aloud:

```text
No output can mean “no match,” not necessarily “error.”
```

---

# Drill Set C: Find by wildcard pattern

Run:

```bash
find . -name "*.txt"
find . -name "*.log"
find . -name "*.sh"
find . -name "*.tmp"
```

Important: keep the pattern quoted.

Say aloud:

```text
Quote find patterns so the shell does not expand them before find sees them.
```

Bad habit:

```bash
find . -name *.txt
```

Good habit:

```bash
find . -name "*.txt"
```

---

# Drill Set D: Case-insensitive name search

Create two files:

```bash
touch docs/README.TXT
touch docs/ReadMe.md
```

Run:

```bash
find . -name "readme*"
find . -iname "readme*"
```

Say aloud:

```text
-name  = case-sensitive
-iname = case-insensitive
```

---

# Drill Set E: Limit search depth

Run:

```bash
find . -maxdepth 1 -print
find . -maxdepth 2 -print
find . -mindepth 2 -type f
```

Say aloud:

```text
-maxdepth limits how deep find descends.
-mindepth ignores shallow matches.
```

---

# Drill Set F: Use `-ls`

Run:

```bash
find . -type f -ls
find . -name "*.sh" -ls
find . -name "*.log" -ls
```

Compare:

```bash
find . -name "*.log" -print
find . -name "*.log" -ls
```

Say aloud:

```text
-print shows paths.
-ls shows detailed file information.
```

---

# Drill Set G: Search by file size

Create files of known sizes:

```bash
dd if=/dev/zero of=tmp/small.bin bs=1K count=1 status=none
dd if=/dev/zero of=tmp/medium.bin bs=1K count=100 status=none
dd if=/dev/zero of=tmp/large.bin bs=1M count=2 status=none
```

Run:

```bash
find . -type f -size +1M
find . -type f -size -2k
find . -type f -size 100k
```

Inspect:

```bash
ls -lh tmp/*.bin
```

Say aloud:

```text
+ means greater than.
- means less than.
No sign means exactly that size unit.
```

---

# Drill Set H: Search by modification time

Create old-looking files:

```bash
touch -d "3 days ago" archive/old-report.txt
touch -d "10 days ago" archive/old-app.log
touch -d "1 hour ago" docs/report.txt
```

Run:

```bash
find . -type f -mtime +7
find . -type f -mtime -1
find . -type f -mmin -120
```

Say aloud:

```text
-mtime works in days.
-mmin works in minutes.
+7 means older than 7 days.
-1 means newer than 1 day.
```

---

# Drill Set I: Use `touch` and `stat`

Inspect:

```bash
stat docs/todo.txt
```

Update its timestamp:

```bash
touch docs/todo.txt
stat docs/todo.txt
```

Set a specific timestamp:

```bash
touch -d "2020-01-01 12:00" docs/todo.txt
stat docs/todo.txt
```

Find it:

```bash
find . -type f -mtime +365
```

Say aloud:

```text
touch can create a file or update its timestamp.
stat shows detailed timestamps.
```

---

# Drill Set J: Search by permission

Inspect script permissions:

```bash
ls -l scripts
```

Run:

```bash
find . -type f -perm 755
find . -type f -perm /111
find . -type f -executable
```

Create a private file:

```bash
touch docs/private.txt
chmod 600 docs/private.txt
```

Run:

```bash
find . -type f -perm 600
find . -type f -perm -600
```

Say aloud:

```text
-perm 600  = exactly 600
-perm -600 = at least these bits are set
-perm /111 = any execute bit is set
```

---

# Drill Set K: Search by owner

Run:

```bash
find . -user "$USER"
```

If safe and allowed, create a root-owned file:

```bash
sudo touch tmp/root-owned.txt
sudo chown root:root tmp/root-owned.txt
```

Run:

```bash
find . -user root -ls
find . -not -user "$USER" -ls
```

Clean it up:

```bash
sudo rm -f tmp/root-owned.txt
```

Say aloud:

```text
-user finds files by owner.
-not reverses a test.
```

---

# Drill Set L: Combine tests with implicit AND

Run:

```bash
find . -type f -name "*.log"
find . -type f -name "*.log" -size -10k
find . -type f -name "*.sh" -perm /111
```

Say aloud:

```text
Multiple find tests are usually ANDed together.
```

Meaning:

```bash
find . -type f -name "*.log"
```

means:

```text
Find things that are files AND have names ending in .log.
```

---

# Drill Set M: Use `-or`

Run:

```bash
find . \( -name "*.txt" -or -name "*.md" \) -type f
find . \( -name "*.log" -or -name "*.tmp" \) -type f
```

Say aloud:

```text
Use escaped parentheses to group find expressions.
-or means either condition may match.
```

Important: parentheses must be protected from the shell:

```bash
\( ... \)
```

---

# Drill Set N: Use `-not`

Run:

```bash
find . -type f -not -name "*.txt"
find . -type f -not -name "*.log"
find . -type f -not -perm /111
```

Say aloud:

```text
-not reverses the next test.
```

---

# Drill Set O: Use `-exec` safely

Run:

```bash
find . -name "*.tmp" -exec ls -l {} \;
```

Then:

```bash
find . -name "*.tmp" -exec rm -i {} \;
```

Answer `n` first. Then run again and answer `y` for one file only.

Recreate the files:

```bash
touch tmp/junk1.tmp tmp/junk2.tmp logs/debug.tmp
```

Say aloud:

```text
{} is replaced by the found pathname.
\; ends the -exec command.
Use rm -i while learning.
```

---

# Drill Set P: Use `-exec ... +`

Run:

```bash
find . -name "*.log" -exec echo FOUND {} \;
find . -name "*.log" -exec echo FOUND {} +
```

Say aloud:

```text
\; runs the command once per file.
+ groups many found files into fewer command runs.
```

---

# Drill Set Q: Use `xargs`

Run:

```bash
find . -name "*.log" -print | xargs ls -l
find . -name "*.txt" -print | xargs wc -l
```

Create a file with a space in its name:

```bash
touch "docs/file with spaces.txt"
```

Bad version:

```bash
find . -name "*.txt" -print | xargs ls -l
```

Safer version:

```bash
find . -name "*.txt" -print0 | xargs -0 ls -l
```

Say aloud:

```text
Use -print0 and xargs -0 when filenames may contain spaces.
```

---

# Drill Set R: Use `locate`

Try:

```bash
locate bash
```

If `locate` is not installed, install it on Debian or Ubuntu:

```bash
sudo apt update
sudo apt install plocate
```

On AlmaLinux/RHEL family:

```bash
sudo dnf install mlocate
```

Update the locate database:

```bash
sudo updatedb
```

Now run:

```bash
locate bash | head
locate "*.conf" | head
```

Say aloud:

```text
locate searches a database.
find searches the filesystem directly.
updatedb refreshes the locate database.
```

---

# Drill Set S: Compare `find` and `locate`

Create a new file:

```bash
touch ~/tlcl-ch17-drills/new-file-for-locate-test.txt
```

Run:

```bash
find ~/tlcl-ch17-drills -name "new-file-for-locate-test.txt"
locate new-file-for-locate-test.txt
```

If `locate` does not find it, run:

```bash
sudo updatedb
locate new-file-for-locate-test.txt
```

Say aloud:

```text
find sees current filesystem state.
locate depends on its database being current.
```

---

# Drill Set T: Troubleshooting practice

Without looking above, answer with commands.

1. Find all `.log` files under `~/tlcl-ch17-drills`.
2. Find all executable ordinary files.
3. Find all files modified in the last two hours.
4. Find all files larger than 1 MB.
5. Find all `.tmp` files and show details with `ls -l`.
6. Find all `.txt` or `.md` files.
7. Find files not owned by you.
8. Find files named `report.txt` case-insensitively.
9. Find all files with exact mode `600`.
10. Find all `.txt` files and count their lines safely even if filenames contain spaces.

Expected command patterns:

```bash
find ~/tlcl-ch17-drills -type f -name "*.log"
find ~/tlcl-ch17-drills -type f -executable
find ~/tlcl-ch17-drills -type f -mmin -120
find ~/tlcl-ch17-drills -type f -size +1M
find ~/tlcl-ch17-drills -type f -name "*.tmp" -exec ls -l {} \;
find ~/tlcl-ch17-drills -type f \( -name "*.txt" -or -name "*.md" \)
find ~/tlcl-ch17-drills -type f -not -user "$USER"
find ~/tlcl-ch17-drills -type f -iname "report.txt"
find ~/tlcl-ch17-drills -type f -perm 600
find ~/tlcl-ch17-drills -type f -name "*.txt" -print0 | xargs -0 wc -l
```

---

# Two-minute rapid drill

Use this when there is little time:

```bash
cd ~/tlcl-ch17-drills

find . -type f
find . -type d
find . -name "*.txt"
find . -iname "readme*"
find . -type f -size +1M
find . -type f -mmin -120
find . -type f -perm /111
find . -type f -name "*.log" -ls
```

---

# Five-minute daily rotation

| Day | Drill sets |
|---|---|
| Monday | A, B, C, D |
| Tuesday | E, F, G |
| Wednesday | H, I, J |
| Thursday | L, M, N |
| Friday | O, P, Q |
| Saturday | R, S |
| Sunday | T or rapid drill |

---

# Commands and syntax to retain

## Core commands

```text
find
locate
updatedb
touch
stat
xargs
```

## Core `find` tests

```text
-name
-iname
-type
-size
-mtime
-mmin
-user
-perm
-executable
-maxdepth
-mindepth
```

## Core `find` logic

```text
-not
-or
\( ... \)
```

## Core `find` actions

```text
-print
-ls
-exec ... {} \;
-exec ... {} +
-delete
```

## High-risk command

Be careful with:

```bash
find . -name "*.tmp" -delete
```

Before using `-delete`, first run the same search with `-print`:

```bash
find . -name "*.tmp" -print
```

Only delete after confirming the matched files are correct.

---

# Retention rule

For Chapter 17, the student should not merely memorize commands. He should practice the search pattern:

```text
Where should I search?
What type of item do I want?
What condition should it match?
What action should I take?
How do I verify before doing anything destructive?
```

That becomes:

```bash
find WHERE TESTS ACTION
```

Example:

```bash
find ~/tlcl-ch17-drills -type f -name "*.log" -ls
```

That structure is the main habit to retain.
