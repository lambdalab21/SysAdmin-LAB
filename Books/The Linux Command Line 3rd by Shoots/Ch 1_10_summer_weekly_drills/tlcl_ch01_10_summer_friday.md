#Book/The-Linux-Command-Line  #Author/Shotts 
# The Linux Command Line, Chapters 1–10: Summer Retention Drills

Use these on `playground01`. Repeat weekly through the summer. Keep each session short: 20–35 minutes.

Rules: predict first, run the command, explain the output aloud, clean up, and do not use the mouse.

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
# Friday: Permissions

Focus: Chapter 9.

## 1. Inspect identity

```bash
id
whoami
groups
```

## 2. Read permission strings

```bash
cd ~/tlcl-summer-drills
ls -l alpha/fruits.txt
ls -ld alpha
ls -l bin/hello.sh
```

For each output, identify file type, owner permissions, group permissions, other permissions, owner, and group.

## 3. Numeric `chmod`

```bash
touch friday-perm.txt
chmod 600 friday-perm.txt
ls -l friday-perm.txt
chmod 644 friday-perm.txt
ls -l friday-perm.txt
chmod 640 friday-perm.txt
ls -l friday-perm.txt
```

## 4. Symbolic `chmod`

```bash
chmod u+x friday-perm.txt
ls -l friday-perm.txt
chmod g+w friday-perm.txt
ls -l friday-perm.txt
chmod o-rwx friday-perm.txt
ls -l friday-perm.txt
chmod u=rw,g=r,o= friday-perm.txt
ls -l friday-perm.txt
```

## 5. File execute permission

```bash
cat > friday-script.sh <<'EOF'
#!/usr/bin/env bash
echo "Friday script ran"
EOF
./friday-script.sh
chmod 755 friday-script.sh
./friday-script.sh
```

## 6. Directory execute permission

```bash
mkdir -p friday-dir
echo "secret" > friday-dir/secret.txt
chmod 600 friday-dir
ls friday-dir
cat friday-dir/secret.txt
chmod 700 friday-dir
ls friday-dir
cat friday-dir/secret.txt
```

## 7. `umask`

```bash
umask
rm -f umask-file
rm -rf umask-dir
umask 0022
touch umask-file
mkdir umask-dir
ls -l umask-file
ls -ld umask-dir
```

## Cleanup

```bash
cd ~/tlcl-summer-drills
rm -f friday-perm.txt friday-script.sh umask-file
rm -rf friday-dir umask-dir
umask 0022
```
