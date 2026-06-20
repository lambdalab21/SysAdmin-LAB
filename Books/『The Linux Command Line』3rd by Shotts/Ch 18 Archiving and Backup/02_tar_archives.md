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
Answer:

1. What is the difference between copying a directory and archiving it? Copying duplicates files and folders individually while archiving bundles them into a single file. 
2. Why should you list an archive before extracting it? To see contents and paths to avoid unexpected outcomes. 
3. Why is extracting into a separate restore directory safer? It prevents accidental overwriting of original files if the archive contains unexpected structure. 
4. What do `c`, `t`, and `x` mean in `tar`? 
C: Create(New Archive)
T: Table(List)
X: Extract

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
No. It only archived project/config. 
---

## Checkpoint

1. What does `tar cf archive.tar directory` do? It creates an archive of the directory. 
2. What does `tar tf archive.tar` do? It lists contents of the archive. 
3. What does `tar xf archive.tar` do? It extracts the archive. 
4. Why should extraction be tested in a restore directory? Extraction should be tested in a restore directory to avoid overwriting originals. 
5. Why is `tar` not automatically compression? Tar enables automatic compression(Not "automatically compression".) because it focuses on building. Compression is optional and separate. 