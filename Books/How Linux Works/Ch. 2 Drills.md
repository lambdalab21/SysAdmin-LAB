 #Book/How-Linux-Works 

Ward Ch. 2 is about basic commands and the Linux directory hierarchy. The goal is not to memorize commands mechanically. He should become comfortable moving through a Linux system, identifying files, using documentation, and explaining where things live.

# How to use these drills

Use 10–15 minutes per session.

For each command, require him to answer:

```text
What does this command do?
What path is being used?
Is the path absolute or relative?
What output should I expect?
What could go wrong?
```

Do not run every drill every day. Rotate them.

# Daily core drill

## Drill 1: Navigate without guessing

Run:

```bash
pwd
ls
ls -l
ls -la
cd /
pwd
cd /etc
pwd
cd ..
pwd
cd ~
pwd
cd -
pwd
```

Questions:

1. What does `pwd` show?
    
2. What is the difference between `/`, `~`, `..`, and `-`?
    
3. What is an absolute path?
    
4. What is a relative path?
    
5. What does `cd -` do?
    

Expected understanding:

```text
/        root directory
~        current user’s home directory
..       parent directory
.        current directory
cd -     previous directory
```

---

## Drill 2: Compare absolute and relative paths

Create a practice directory:

```bash
mkdir -p ~/hlw-ch2/practice/subdir
cd ~/hlw-ch2/practice
touch file1.txt
touch subdir/file2.txt
```

Use both forms:

```bash
ls ~/hlw-ch2/practice
ls .
ls subdir
ls ./subdir
ls ~/hlw-ch2/practice/subdir
```

Questions:

1. Which paths are absolute?
    
2. Which paths are relative?
    
3. Why do `subdir` and `./subdir` work from the current directory?
    
4. Why might a script prefer an absolute path?
    

---

## Drill 3: Explore the root filesystem

Run:

```bash
ls /
```

Then inspect:

```bash
ls /etc
ls /var
ls /home
ls /usr
ls /tmp
ls /dev
ls /proc
```

He should explain these directories:

|Directory|Main purpose|
|---|---|
|`/etc`|System configuration|
|`/var`|Variable data such as logs, caches, and changing application files|
|`/home`|Normal users’ home directories|
|`/usr`|Most installed user-space programs, libraries, and shared data|
|`/tmp`|Temporary files|
|`/dev`|Device files|
|`/proc`|Kernel and process information exposed as a virtual filesystem|
|`/root`|Root user’s home directory|
|`/boot`|Boot-related files|

Do not require perfect memorization immediately. Require recognition.

---

# File and directory manipulation drills

## Drill 4: Create, copy, rename, and remove files

Run:

```bash
mkdir -p ~/hlw-ch2/files
cd ~/hlw-ch2/files

touch alpha.txt
mkdir archive
cp alpha.txt archive/
mv alpha.txt beta.txt
ls -l
ls -l archive
rm beta.txt
rm archive/alpha.txt
rmdir archive
```

Questions:

1. What is the difference between `cp` and `mv`?
    
2. When does `mv` rename a file?
    
3. When does `mv` move a file?
    
4. Why did `rmdir` work only after the directory became empty?
    
5. When is `rm -r` needed?
    

---

## Drill 5: Learn safe deletion habits

Create:

```bash
mkdir -p ~/hlw-ch2/delete-test/subdir
touch ~/hlw-ch2/delete-test/file1
touch ~/hlw-ch2/delete-test/subdir/file2
```

Inspect first:

```bash
find ~/hlw-ch2/delete-test -print
```

Then remove carefully:

```bash
rm ~/hlw-ch2/delete-test/file1
rm -r ~/hlw-ch2/delete-test/subdir
rmdir ~/hlw-ch2/delete-test
```

Questions:

1. Why inspect before deleting?
    
2. Why is `rm -r` dangerous?
    
3. Why should he be careful with wildcards and absolute paths?
    
4. Why is this command dangerous?
    

```bash
rm -rf *
```

Expected answer:

> It recursively deletes everything matched in the current directory without prompting. A wrong working directory can destroy important data.

---

## Drill 6: Use wildcards

Create:

```bash
mkdir -p ~/hlw-ch2/glob-test
cd ~/hlw-ch2/glob-test

touch report1.txt report2.txt report-final.txt
touch image1.png image2.png notes.md
```

Run:

```bash
ls *.txt
ls report?.txt
ls report*
ls *.png
ls *final*
```

Questions:

1. What does `*` match?
    
2. What does `?` match?
    
3. Which command matches `report1.txt` but not `report-final.txt`?
    
4. Why should he inspect wildcard matches before using `rm`?
    

Safe pattern:

```bash
ls *.txt
rm *.txt
```

First inspect. Then delete.

---

# Command lookup and documentation drills

## Drill 7: Find what kind of command he is running

Run:

```bash
type cd
type ls
type grep
type python3
type ll
```

Questions:

1. Is `cd` an external executable?
    
2. What is a shell builtin?
    
3. What is an alias?
    
4. Why might `type` be more useful than `which`?
    

Then:

```bash
which ls
which python3
command -v ls
command -v cd
```

Expected understanding:

- `type` explains shell interpretation.
    
- `which` searches executable paths.
    
- `command -v` is often a better general lookup command.
    

---

## Drill 8: Use man pages efficiently

Run:

```bash
man ls
```

Inside `man`:

```text
/hidden
n
q
```

Then:

```bash
man cp
man mv
man rm
man mkdir
```

Questions:

1. How do you search inside a man page?
    
2. What does `n` do after a search?
    
3. Why should he look up flags rather than trust memory?
    
4. Why should he check `rm` before using unfamiliar options?
    

Mini-task:

Find the meaning of:

```bash
ls -a
ls -l
cp -r
rm -r
mkdir -p
```

---

## Drill 9: Compare `man`, `info`, and shell help

Run:

```bash
help cd
man ls
info coreutils 'ls invocation'
```

Questions:

1. Why does `help cd` work?
    
2. Why does `man cd` sometimes not explain shell-specific behavior well?
    
3. When is `info` useful?
    
4. Why does documentation differ between shell builtins and external commands?
    

---

# File inspection drills

## Drill 10: Identify file types

Run:

```bash
file /bin/ls
file /etc/passwd
file /dev/null
file /proc/cpuinfo
file ~/hlw-ch2/files
```

Questions:

1. Is `/bin/ls` a text file?
    
2. What type of file is `/etc/passwd`?
    
3. What is special about `/dev/null`?
    
4. Is `/proc/cpuinfo` a normal file stored on disk?
    
5. Why is `file` useful before opening an unfamiliar file?
    

---

## Drill 11: Inspect text safely

Run:

```bash
cat /etc/hostname
less /etc/passwd
head /etc/passwd
tail /etc/passwd
wc -l /etc/passwd
```

Questions:

1. When is `cat` fine?
    
2. Why is `less` better for large files?
    
3. What does `head` show?
    
4. What does `tail` show?
    
5. What does `wc -l` count?
    

---

## Drill 12: Search inside files

Run:

```bash
grep root /etc/passwd
grep bash /etc/passwd
grep -n root /etc/passwd
grep -i root /etc/passwd
```

Questions:

1. What does `grep` do?
    
2. What does `-n` add?
    
3. What does `-i` change?
    
4. Why is searching better than opening a long file and scrolling randomly?
    

---

# Links drills

## Drill 13: Create a symbolic link

Run:

```bash
mkdir -p ~/hlw-ch2/links
cd ~/hlw-ch2/links

printf 'original\n' > original.txt
ln -s original.txt shortcut.txt
ls -l
cat shortcut.txt
```

Then:

```bash
rm original.txt
cat shortcut.txt
```

Questions:

1. What does `ln -s` create?
    
2. Why did the symbolic link stop working after deleting the original?
    
3. How does `ls -l` show a symlink?
    
4. Why are symlinks useful?
    

Fix:

```bash
printf 'restored\n' > original.txt
cat shortcut.txt
```

---

## Drill 14: Create a hard link

Run:

```bash
cd ~/hlw-ch2/links

printf 'hard link test\n' > source.txt
ln source.txt hardlink.txt
ls -li source.txt hardlink.txt
```

Then:

```bash
rm source.txt
cat hardlink.txt
```

Questions:

1. Why did `hardlink.txt` still work?
    
2. What did `ls -li` reveal?
    
3. What is an inode?
    
4. How is a hard link different from a symbolic link?
    

Expected understanding:

```text
symbolic link = points to a pathname
hard link     = another directory entry for the same inode
```

---

# Process and `/proc` preview drills

## Drill 15: Connect Ch. 2 to Ch. 8

Run:

```bash
echo $$
ls /proc/$$
cat /proc/$$/status | head
```

Questions:

1. What does `$$` represent?
    
2. Why does `/proc/<PID>` exist?
    
3. Is `/proc` an ordinary directory stored on disk?
    
4. How does this connect filesystem navigation to process inspection?
    

This drill is useful because Ch. 2 is not isolated from later chapters.

---

# Device-file preview drills

## Drill 16: Use `/dev/null`

Run:

```bash
echo "hello" > /dev/null
cat /dev/null
```

Then:

```bash
yes > /dev/null &
PID=$!
sleep 2
kill "$PID"
```

Questions:

1. What happens to data written to `/dev/null`?
    
2. Why might commands redirect output there?
    
3. Why did `yes > /dev/null` consume CPU?
    
4. How does this connect to Ch. 8 CPU experiments?
    

---

# Directory hierarchy scavenger hunt

Give him these tasks without commands.

## Drill 17: Find the answer

Ask him to locate:

1. His home directory.
    
2. Root user’s home directory.
    
3. SSH server configuration.
    
4. Nginx configuration directory.
    
5. System logs.
    
6. User account database.
    
7. Group database.
    
8. Current hostname file.
    
9. Device files.
    
10. Process information.
    

Expected paths:

```text
/home/<user>
/root
/etc/ssh/sshd_config
/etc/nginx
/var/log
/etc/passwd
/etc/group
/etc/hostname
/dev
/proc
```

Then ask:

> Which of these are configuration files, changing data, virtual filesystems, or user-owned files?

---

# Five-minute recall drills

## Drill A: Explain each path

Without notes, explain:

```text
/
/etc
/var
/home
/tmp
/usr
/dev
/proc
/root
/boot
```

## Drill B: Explain each command

Without notes:

```text
pwd
cd
ls
cp
mv
rm
mkdir
rmdir
ln
file
less
grep
type
which
man
```

## Drill C: Explain the difference

```text
absolute path vs relative path
file vs directory
copy vs move
hard link vs symbolic link
shell builtin vs external command
/dev vs /proc
/etc vs /var
rm vs rmdir
```

---

# Mystery challenges

## Challenge 1: Broken symbolic link

Setup:

```bash
mkdir -p ~/hlw-ch2/mystery1
cd ~/hlw-ch2/mystery1

printf 'hello\n' > original.txt
ln -s original.txt shortcut.txt
rm original.txt
```

Question:

> Why does `cat shortcut.txt` fail?

Useful commands:

```bash
ls -l
file shortcut.txt
```

---

## Challenge 2: Wrong working directory

Setup:

```bash
mkdir -p ~/hlw-ch2/mystery2/a
mkdir -p ~/hlw-ch2/mystery2/b
touch ~/hlw-ch2/mystery2/a/test.txt
```

Ask him to find `test.txt` starting from:

```bash
cd ~/hlw-ch2/mystery2/b
```

Useful commands:

```bash
pwd
ls
ls ..
find .. -name test.txt
```

---

## Challenge 3: Command confusion

Ask:

> Why does this work?

```bash
cd /
```

but this may not show a filesystem path:

```bash
which cd
```

Expected answer:

> `cd` is a shell builtin, not an external executable found in PATH.

Useful command:

```bash
type cd
```

---

## Challenge 4: Dangerous wildcard

Create:

```bash
mkdir -p ~/hlw-ch2/mystery4
cd ~/hlw-ch2/mystery4

touch report1.txt report2.txt keep.txt
```

Ask:

> How do you delete only the report files safely?

Expected sequence:

```bash
ls report*.txt
rm report*.txt
ls
```

Do not accept:

```bash
rm *
```

---

# Retention schedule

|Day|Focus|
|---|---|
|Day 1|Navigation and directory hierarchy|
|Day 2|File creation, copy, move, deletion|
|Day 3|Wildcards and safe deletion|
|Day 4|Command lookup and man pages|
|Day 5|File inspection and `grep`|
|Day 6|Symbolic links and hard links|
|Day 7|Scavenger hunt without notes|
|Day 10|Repeat mystery challenges|
|Day 14|Five-minute oral review|
|Day 30|Full filesystem scavenger hunt|

# Exit criteria

He is ready to move on when he can do these without instructions:

```bash
pwd
cd
ls -la
mkdir -p
cp
mv
rm
rmdir
ln -s
file
less
grep
type
command -v
man
```

And explain:

1. Absolute vs relative path.
    
2. `/etc` vs `/var`.
    
3. `/dev` vs `/proc`.
    
4. File vs directory permissions at a basic level.
    
5. Symbolic link vs hard link.
    
6. Shell builtin vs external command.
    
7. Why `rm -r` and wildcards require caution.
    
8. Why `man` is a normal part of using Linux.
    

The main habit to build is:

```text
locate → inspect → predict → run → verify
```

That is the Ch. 2 foundation he will use later for SSH, Nginx, `scp`, permissions, networking, and troubleshooting.