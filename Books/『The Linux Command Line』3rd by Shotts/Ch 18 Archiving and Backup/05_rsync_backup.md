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
# Day 5: Backup and Synchronization with `rsync`

## Read these Chapter 18 sections

Read the parts covering:

```text
rsync
copying directories
synchronizing changes
dry runs if discussed
remote copy if discussed
```

Focus on local practice first.

---

## Feynman analogy

`tar` is like putting everything into a box.

`rsync` is like a careful helper who compares two shelves and copies only what is needed.

```text
rsync = synchronize source and destination
```

This makes it useful for repeated backups.

---

## Before touching the keyboard

Answer:

1. What is the source?
2. What is the destination?
3. Why does the trailing slash matter in many `rsync` examples?
4. Why is a dry run useful?
5. Why is `--delete` dangerous?

---

## Practice

```bash
cd ~/tlcl-ch18-lab
mkdir -p backup/rsync-copy
```

Copy the project directory into backup:

```bash
rsync -av project backup/rsync-copy/
find backup/rsync-copy -type f -print
```

Now run the same command again:

```bash
rsync -av project backup/rsync-copy/
```

Question:

```text
Did rsync copy everything again, or did it detect little/no change?
```

---

## Drill 1: Modify and sync again

Change a file:

```bash
echo 'new setting=true' >> project/config/app.conf
```

Run:

```bash
rsync -av project backup/rsync-copy/
```

Verify:

```bash
cat backup/rsync-copy/project/config/app.conf
```

Say aloud:

```text
rsync is useful for repeated backups because it notices changes.
```

---

## Drill 2: Dry run

Run:

```bash
rsync -av --dry-run project backup/rsync-dry-run/
```

Question:

```text
Did it actually copy files?
```

Expected answer:

```text
No. --dry-run previews what would happen.
```

---

## Drill 3: Trailing slash discipline

Create two destinations:

```bash
mkdir -p backup/slash-test-a backup/slash-test-b
```

Run:

```bash
rsync -av project backup/slash-test-a/
rsync -av project/ backup/slash-test-b/
```

Inspect:

```bash
find backup/slash-test-a -maxdepth 3 -type f -print
find backup/slash-test-b -maxdepth 3 -type f -print
```

Question:

```text
What changed when the source ended with a slash?
```

Expected idea:

```text
Without trailing slash, rsync copied the project directory itself.
With trailing slash, rsync copied the contents of project.
```

---

## Drill 4: The `--delete` warning

Do not run this until you understand it:

```bash
rsync -av --delete SOURCE/ DEST/
```

Say aloud:

```text
--delete can remove files from the destination that are not in the source.
Always use --dry-run first.
```

Safe preview only:

```bash
rsync -av --delete --dry-run project/ backup/slash-test-b/
```

---

## Disciplined thinking checkpoint

Before `rsync`, say:

```text
Source is...
Destination is...
Trailing slash means...
I will dry-run first if deletion is involved.
I will verify destination after copying.
```

---

## Checkpoint

He understands Day 5 only if he can answer:

1. What does `rsync -av` do?
2. Why is `rsync` good for repeated backups?
3. What does `--dry-run` do?
4. Why is `--delete` dangerous?
5. What is the trailing slash issue?

---

## Cleanup

No cleanup needed.
