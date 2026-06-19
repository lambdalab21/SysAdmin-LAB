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
# Day 3: Compressed Tar Archives

## Read these Chapter 18 sections

Read the parts covering compressed archives such as:

```text
.tar.gz
.tgz
.tar.bz2
```

Focus on:

```text
tar + gzip
tar + bzip2
listing before extracting
restoring into a separate directory
```

---

## Feynman analogy

Yesterday, `tar` put many files into a cardboard box.

Today, compression squeezes the box smaller.

```text
tar = box the files
gzip/bzip2 = squeeze the box
```

A `.tar.gz` file means:

```text
a tar archive compressed with gzip
```

---

## Before touching the keyboard

Answer:

1. What does `.tar` suggest?
2. What does `.gz` suggest?
3. What does `.tar.gz` suggest?
4. Why is it dangerous to extract an archive without knowing its contents?

---

## Practice

```bash
cd ~/tlcl-ch18-lab
```

Create a gzip-compressed tar archive:

```bash
tar czf backup/project.tar.gz project
ls -lh backup/project.tar.gz
```

List it:

```bash
tar tzf backup/project.tar.gz
```

Extract safely:

```bash
mkdir -p restore/day3-gz
cd restore/day3-gz
tar xzf ../../backup/project.tar.gz
find . -type f -print
```

Verify:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/day3-gz/project
```

---

## Drill 1: Create a bzip2-compressed tar archive

Run:

```bash
cd ~/tlcl-ch18-lab
tar cjf backup/project.tar.bz2 project
ls -lh backup/project.tar.bz2
tar tjf backup/project.tar.bz2
```

Extract:

```bash
mkdir -p restore/day3-bz2
cd restore/day3-bz2
tar xjf ../../backup/project.tar.bz2
```

Verify:

```bash
cd ~/tlcl-ch18-lab
diff -r project restore/day3-bz2/project
```

---

## Drill 2: Decode the command letters

Explain:

```bash
tar czf backup/project.tar.gz project
```

Answer:

```text
c =
z =
f =
backup/project.tar.gz =
project =
```

Then explain:

```bash
tar xzf backup/project.tar.gz
```

Answer:

```text
x =
z =
f =
```

---

## Disciplined thinking checkpoint

A careless student says:

```text
I made a backup.
```

A disciplined student says:

```text
I created the archive.
I listed its contents.
I extracted it into restore/day3.
I compared it with the original.
```

---

## Checkpoint

He understands Day 3 only if he can answer:

1. What does `.tar.gz` mean?
2. What does `tar czf` do?
3. What does `tar xzf` do?
4. What does `tar cjf` do?
5. Why should he use `diff -r` after restore?

---

## Cleanup

Keep the archives for Day 6.
