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
# Day 6: Restore Practice and Backup Discipline

## Purpose

This day is the most important day.

A student who can create archives but cannot restore them does not understand backup.

---

## Feynman analogy

A backup is like a spare key.

It is not enough to say:

```text
I think I made a spare key.
```

You must test:

```text
Can the spare key open the door?
```

In Linux terms:

```text
Can I restore the files into a separate directory and verify them?
```

---

## Before touching the keyboard

Answer:

1. Which backup files did I create this week?
2. How do I list each one?
3. How do I extract each one safely?
4. How do I compare restored files with originals?
5. Which command is most dangerous in this chapter?

---

# Restore challenge 1: tar archive

Run:

```bash
cd ~/tlcl-ch18-lab
rm -rf restore/final-tar
mkdir -p restore/final-tar
cd restore/final-tar
tar xf ../../backup/project.tar
```

Verify:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/final-tar/project
```

---

# Restore challenge 2: gzip-compressed tar archive

Run:

```bash
cd ~/tlcl-ch18-lab
rm -rf restore/final-targz
mkdir -p restore/final-targz
cd restore/final-targz
tar xzf ../../backup/project.tar.gz
```

Verify:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/final-targz/project
```

If there is a difference because you changed `project/config/app.conf` during the `rsync` day, explain the difference. That is good evidence, not failure.

---

# Restore challenge 3: zip archive

Run:

```bash
cd ~/tlcl-ch18-lab
rm -rf restore/final-zip
mkdir -p restore/final-zip
unzip backup/project.zip -d restore/final-zip
```

Verify:

```bash
diff -r project restore/final-zip/project
```

Again, if the original changed after the zip was created, explain the difference.

---

# Restore challenge 4: rsync backup

Run:

```bash
cd ~/tlcl-ch18-lab
rm -rf restore/final-rsync
mkdir -p restore/final-rsync
rsync -av backup/rsync-copy/project restore/final-rsync/
```

Verify:

```bash
diff -r backup/rsync-copy/project restore/final-rsync/project
```

---

# Disciplined-thinking audit

Fill this out:

```text
Archive or backup tested:
Command used to list or inspect:
Command used to restore:
Restore location:
Verification command:
Result:
Difference found:
Explanation:
```

Do this for:

```text
project.tar
project.tar.gz
project.zip
rsync-copy
```

---

# Dangerous command review

Explain why these can be dangerous:

```bash
rm -rf restore
tar xf archive.tar
unzip archive.zip
rsync -av --delete source/ dest/
```

Expected ideas:

```text
rm -rf can delete trees quickly.
tar/unzip can overwrite files if extracted carelessly.
rsync --delete can remove destination files.
```

---

# Final self-test

Without notes, answer:

1. Compress one file with gzip.
2. Restore a gzip file.
3. Create a tar archive of a directory.
4. List a tar archive.
5. Extract a tar archive.
6. Create a `.tar.gz`.
7. List a `.tar.gz`.
8. Create a zip recursively.
9. List a zip archive.
10. Use rsync to copy a directory.
11. Preview an rsync command without copying.
12. Verify two directories match.

Expected command patterns:

```bash
gzip file.txt
gunzip file.txt.gz
tar cf archive.tar directory
tar tf archive.tar
tar xf archive.tar
tar czf archive.tar.gz directory
tar tzf archive.tar.gz
zip -r archive.zip directory
unzip -l archive.zip
rsync -av source dest
rsync -av --dry-run source dest
diff -r original restored
```

---

# End standard

He understands Chapter 18 only if he can say:

```text
I know whether I am compressing, archiving, or backing up.
I can list what is inside an archive.
I can restore into a safe directory.
I can compare restored files with the original.
I do not trust a backup until I restore-test it.
```
