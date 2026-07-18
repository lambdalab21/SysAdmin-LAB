#Book/The-Linux-Command-Line #Author/Shotts 
#archive
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

## Before touching the keyboard

Answer:

1. Why might `zip` be more convenient than `tar` when sharing with non-Linux users? Zip is supported on both windows and macOS while  tar requires extra software. 
2. Why should you list a zip file before extracting it? To see  what files and paths are inside. 
3. What does recursive mean? Includes  the directory and all of it's subdirectores/files. 
4. Why is restoring into a separate directory still important? Prevents overwriting of original files if the archive contains the same paths. 

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

Before sending a zip file to someone else, ask:

```text
What files did I include?
Did I include secrets?
Did I include unnecessary logs?
Can I list the archive?
Can someone else extract it?
```

---

## Checkpoint

You understand Day 4 only if you can answer:

1. What does `zip -r` do?
2. What does `unzip -l` do?
3. What does `unzip file.zip -d DIR` do?
4. Why is zip useful for sharing?
5. Why can zip files accidentally include private files?
