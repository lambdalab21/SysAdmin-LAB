#Book/The-Linux-Command-Line  #Author/Shotts 
# The Linux Command Line, Chapters 1–10: Summer Retention Drills

## One-time setup

```bash
mkdir -p ~/tlcl-summer-drills/{alpha,beta,gamma,logs,bin,tmp}
cd ~/tlcl-summer-drills
printf 'apple\nbanana\napple\norange\n' > alpha/fruits.txt
printf 'red\nblue\ngreen\nred\n' > beta/colors.txt
printf 'error: failed login\ninfo: started\nerror: timeout\n' > logs/app.log
echo '#!/usr/bin/env bash' > bin/hello.sh
echo 'echo "hello from $0"' >> bin/hello.sh
chmod 755 bin/hello.sh
touch alpha/a.txt alpha/b.txt beta/c.txt gamma/d.md tmp/junk.tmp
find ~/tlcl-summer-drills -maxdepth 2 -type f -print
```

---
# Tuesday: Manipulating Files and Directories

Focus: Chapter 4, plus safe redirection preview.

## 1. Create a safe workspace

```bash
cd ~/tlcl-summer-drills
rm -rf tuesday-practice
mkdir -p tuesday-practice/{src,dst,backup}
cd tuesday-practice
find . -maxdepth 2 -type d -print
```

## 2. Create files

```bash
touch src/file1.txt src/file2.txt src/file3.log
ls -l src
```

## 3. Copy files

```bash
cp src/file1.txt dst/
cp src/file2.txt dst/file2-copy.txt
ls -l dst
```

## 4. Copy directories recursively

```bash
cp -r src backup/src-copy
find backup -maxdepth 3 -type f -print
```

## 5. Move and rename

```bash
mv dst/file2-copy.txt dst/renamed-file2.txt
mv src/file3.log dst/
ls -l src dst
```

## 6. Remove safely

```bash
find . -type f -print
rm dst/renamed-file2.txt
find . -type f -print
rm -r backup/src-copy
find . -maxdepth 3 -print
```

## 7. Wildcards carefully

```bash
touch src/a.txt src/b.txt src/c.log src/d.log
ls src/*.txt
ls src/*.log
cp src/*.txt dst/
ls -l dst
```

Rule: preview glob matches before using them with `rm` or `mv`.

## 8. Redirection creates and overwrites

```bash
echo "first" > dst/output.txt
cat dst/output.txt
echo "second" > dst/output.txt
cat dst/output.txt
echo "third" >> dst/output.txt
cat dst/output.txt
```

## Cleanup

```bash
cd ~/tlcl-summer-drills
rm -rf tuesday-practice
```
