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
# Day 1: Compression with `gzip` and `bzip2`

## Read these Chapter 18 sections

Read the parts covering:

```text
gzip
gunzip
zcat
bzip2
bunzip2
bzcat
```

Focus on the concept first, not every option.

---

## Feynman analogy

Imagine one sheet of paper with repeated words.

Compression is like folding that sheet more efficiently.

But it is still one sheet.

```text
gzip  = compress one file
gunzip = restore it
zcat  = view compressed text without fully unpacking it first
```

Compression is not the same as archiving. Compression makes a file smaller. Archiving collects many files together.

---

## Before touching the keyboard

Answer:

1. Does `gzip notes.txt` create a second file or replace the original?
2. What will the compressed filename probably be?
3. Why should you know how to restore before trusting compression?
4. What is the difference between `cat` and `zcat`?

---

## Practice

```bash
cd ~/tlcl-ch18-lab/project
cp notes.txt ../tmp/notes-gzip.txt
cd ../tmp
```

Run:

```bash
ls -l notes-gzip.txt
gzip notes-gzip.txt
ls -l
```

Question:

```text
Where did notes-gzip.txt go?
```

Expected answer:

```text
gzip replaced it with notes-gzip.txt.gz.
```

Restore:

```bash
gunzip notes-gzip.txt.gz
ls -l
cat notes-gzip.txt
```

---

## Drill 1: View compressed text with `zcat`

Run:

```bash
gzip notes-gzip.txt
zcat notes-gzip.txt.gz
gunzip notes-gzip.txt.gz
```

Say aloud:

```text
zcat lets me inspect compressed text.
Inspection is part of backup discipline.
```

---

## Drill 2: Compare `gzip` and `bzip2`

Create copies:

```bash
cp ../project/app.log app-gzip.log
cp ../project/app.log app-bzip2.log
```

Compress:

```bash
gzip app-gzip.log
bzip2 app-bzip2.log
ls -lh
```

View:

```bash
zcat app-gzip.log.gz
bzcat app-bzip2.log.bz2
```

Restore:

```bash
gunzip app-gzip.log.gz
bunzip2 app-bzip2.log.bz2
```

---

## Disciplined thinking checkpoint

For each compressed file, ask:

```text
Can I tell what original file it came from?
Can I view or restore it?
Did I accidentally destroy my only copy?
```

---

## Explain to a ten-year-old

Explain this:

```bash
gzip notes.txt
```

Use:

```text
The file is like...
gzip changes it into...
The original name changes to...
To get it back, I use...
```

---

## Checkpoint

He understands Day 1 only if he can answer:

1. What does `gzip file` do to the original file?
2. What does `gunzip` do?
3. What does `zcat` do?
4. What does `bzip2` create?
5. Why is compression not the same as backup?

---

## Cleanup

```bash
cd ~/tlcl-ch18-lab/tmp
rm -f notes-gzip.txt app-gzip.log app-bzip2.log *.gz *.bz2
```
