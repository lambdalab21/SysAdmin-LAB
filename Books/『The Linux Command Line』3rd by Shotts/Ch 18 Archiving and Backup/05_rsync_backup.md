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

## Before touching the keyboard

Answer:

1. What is the source? The Project directory
2. What is the destination? The backup location. 
3. Why does the trailing slash matter in many `rsync` examples? A trailing slash on the source tells rsync to copy the contents of the directory. Without project, rsync would just copy the directory as a subfolder inside of the destination. 
4. Why is a dry run useful? It shows what rsync would do without making any changes. 
5. Why is `--delete` dangerous? It removes files from the destination that  don't exist in the source. 

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
^ It detected little to no change. Rsync only transferred a small amount of data on the second run. 

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

Safe preview only:

```bash
rsync -av --delete --dry-run project/ backup/slash-test-b/
```

---

## Checkpoint

You understand Day 5 only if you can answer:

1. What does `rsync -av` do? It recursively copies files  and directories from  the source to the destination. 
2. Why is `rsync` good for repeated backups? It copies files that have changed on subsequent runs. 
3. What does `--dry-run` do? It simulates the operation and shows what would happen without making any actual changes. 
4. Why is `--delete` dangerous? It deletes the files in the destination that don't exist in the source. Any mistake can cause data loss. 
5. What is the trailing slash issue? Whether or not you include a trailing slash on the source directory changes the rsync copies the director itself or just its contents inside of the destination. 