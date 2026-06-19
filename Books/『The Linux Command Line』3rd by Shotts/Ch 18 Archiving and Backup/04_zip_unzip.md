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
# Day 4: `zip` and `unzip`

## Read these Chapter 18 sections

Read the parts covering:

```text
zip
unzip
listing zip contents
extracting zip files
```

---

## Feynman analogy

`tar` is common in Unix/Linux.

`zip` is common when exchanging files with Windows, macOS, school systems, and ordinary users.

```text
zip = archive and compress in a widely recognized format
unzip = extract zip files
```

---

## Before touching the keyboard

Answer:

1. Why might `zip` be more convenient than `tar` when sharing with non-Linux users?
2. Why should you list a zip file before extracting it?
3. What does recursive mean?
4. Why is restoring into a separate directory still important?

---

## Practice

```bash
cd ~/tlcl-ch18-lab
```

Create a zip archive recursively:

```bash
zip -r backup/project.zip project
ls -lh backup/project.zip
```

List contents:

```bash
unzip -l backup/project.zip
```

Extract safely:

```bash
mkdir -p restore/day4
cd restore/day4
unzip ../../backup/project.zip
find . -type f -print
```

Verify:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/day4/project
```

---

## Drill 1: Zip only selected files

Run:

```bash
cd ~/tlcl-ch18-lab
zip backup/config.zip project/config/app.conf
unzip -l backup/config.zip
```

Question:

```text
What exactly went into the archive?
```

Expected answer:

```text
Only project/config/app.conf.
```

---

## Drill 2: Extract to a chosen directory

Run:

```bash
mkdir -p restore/day4-selected
unzip backup/config.zip -d restore/day4-selected
find restore/day4-selected -type f -print
```

Say aloud:

```text
-d chooses the extraction directory.
```

---

## Disciplined thinking checkpoint

Before sending a zip file to someone else, ask:

```text
What files did I include?
Did I include secrets?
Did I include unnecessary logs?
Can I list the archive?
Can someone else extract it?
```

---

## Explain to a ten-year-old

Explain:

```bash
zip -r backup/project.zip project
```

Use:

```text
zip is like...
-r means...
project.zip contains...
```

---

## Checkpoint

He understands Day 4 only if he can answer:

1. What does `zip -r` do?
2. What does `unzip -l` do?
3. What does `unzip file.zip -d DIR` do?
4. Why is zip useful for sharing?
5. Why can zip files accidentally include private files?

---

## Cleanup

Keep the zip files for Day 6.
