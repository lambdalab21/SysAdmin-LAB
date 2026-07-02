# The Linux Command Line, Chapter 18: Archiving and Backup

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

## Before touching the keyboard

Answer:

1. What does `.tar` suggest? .tar suggests a tar archive. 
2. What does `.gz` suggest? .gz suggest gzip compressions. 
3. What does `.tar.gz` suggest? .tar.gz suggests a tar archive compressed with gzip. 
4. Why is it dangerous to extract an archive without knowing its contents? Because the archive might have unexpected file or absolute paths that overwrite important system files. 

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
c = Create
z = GZip Compression
f = File
backup/project.tar.gz = The Output Archive File
project = The Input Directory
```

Then explain:

```bash
tar xzf backup/project.tar.gz
```

Answer:

```text
x = Extract
z = GZip Compression
f = File
```

---

## Checkpoint

You understand Day 3 only if you can answer:

1. What does `.tar.gz` mean? Tar Archive compressed with GZip. 
2. What does `tar czf` do? It creates a new gzip-compressed tar archive. 
3. What does `tar xzf` do? It extracts a gzip-compressed tar archive. 
4. What does `tar cjf` do? It creates a new bzip2-compressed tar archive. 
5. Why should he use `diff -r` after restore? Used to compare the original and restored directories to confirm that they're identical. 