# The Linux Command Line, Chapter 18: Archiving and Backup

Chapter 18 is best split into pieces. Do not read it in one sitting and expect the commands to stick.

Main habit:

```text
A backup is not real until you can restore from it.
```

Disciplined thinking pattern for this chapter:

```text
1. What am I trying to preserve?
2. Am I compressing one file or archiving many files?
3. Where is the backup going?
4. How can I list or inspect the backup before trusting it?
5. Can I restore it into a separate directory?
6. Did I verify the restored files?
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch18-lab/{project,backup,restore,tmp}
cd ~/tlcl-ch18-lab/project

cat > notes.txt <<'EOF'
Linux backup practice
This file is plain text.
EOF

cat > app.log <<'EOF'
INFO started
ERROR failed login
INFO stopped
EOF

mkdir -p src docs config
echo 'print("hello")' > src/app.py
echo '# Project README' > docs/README.md
echo 'port=8080' > config/app.conf
```

Verify:

```bash
find ~/tlcl-ch18-lab -maxdepth 3 -type f -print
```

---
# Day 2: Archiving with `tar`

## Read these Chapter 18 sections

Read the parts covering:

```text
tar
creating archives
listing archive contents
extracting archives
```

Focus on:

```text
-c create
-f file
-t list
-x extract
-v verbose
```

---

## Feynman analogy

Imagine putting a whole folder into one cardboard box.

The box is not necessarily smaller. It is just easier to carry.

```text
tar = put many files and directories into one archive file
```

Compression is like squeezing the box smaller. That comes later.

---

## Before touching the keyboard

Answer:

1. What is the difference between copying a directory and archiving it?
2. Why should you list an archive before extracting it?
3. Why is extracting into a separate restore directory safer?
4. What do `c`, `t`, and `x` mean in `tar`?

---

## Practice

```bash
cd ~/tlcl-ch18-lab
```

Create an archive:

```bash
tar cf backup/project.tar project
ls -lh backup/project.tar
```

List contents:

```bash
tar tf backup/project.tar
```

Verbose list:

```bash
tar tvf backup/project.tar
```

Say aloud:

```text
c = create
t = table/list
x = extract
f = archive file
```

---

## Drill 1: Extract safely

Do not extract over the original.

Run:

```bash
mkdir -p restore/day2
cd restore/day2
tar xf ../../backup/project.tar
find . -type f -print
```

Verify:

```bash
cat project/notes.txt
cat project/config/app.conf
```

---

## Drill 2: Compare original and restored files

Run:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/day2/project
```

Expected result:

```text
No output if the directories match.
```

Say aloud:

```text
No diff output means no differences were found.
```

---

## Drill 3: Archive only selected files

Create:

```bash
cd ~/tlcl-ch18-lab
tar cf backup/config-only.tar project/config
tar tf backup/config-only.tar
```

Question:

```text
Did this archive include src/app.py?
```

Expected answer:

```text
No. It archived only project/config.
```

---

## Disciplined thinking checkpoint

Before creating an archive, write:

```text
What exact path am I archiving?
Where will the archive be saved?
How will I list it?
Where will I test extraction?
```

---

## Explain to a ten-year-old

Explain:

```bash
tar cf backup/project.tar project
```

Use:

```text
tar is like a box.
project is...
backup/project.tar is...
The c means...
The f means...
```

---

## Checkpoint

He understands Day 2 only if he can answer:

1. What does `tar cf archive.tar directory` do?
2. What does `tar tf archive.tar` do?
3. What does `tar xf archive.tar` do?
4. Why should extraction be tested in a restore directory?
5. Why is `tar` not automatically compression?

---

## Cleanup

Keep the archive for later days.
