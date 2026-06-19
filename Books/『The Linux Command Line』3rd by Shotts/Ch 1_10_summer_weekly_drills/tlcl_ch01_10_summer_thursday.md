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
# Thursday: Redirection, Pipelines, and Expansion

Focus: Chapters 6 and 7.

## 1. Standard output redirection

```bash
cd ~/tlcl-summer-drills
echo "alpha" > thursday.txt
cat thursday.txt
echo "beta" > thursday.txt
cat thursday.txt
echo "gamma" >> thursday.txt
cat thursday.txt
```

## 2. Standard error redirection

```bash
ls missing-file
ls missing-file 2> errors.txt
cat errors.txt
ls missing-again 2>> errors.txt
cat errors.txt
```

## 3. Redirect stdout and stderr together

```bash
ls alpha missing-file > both.txt 2>&1
cat both.txt
```

Say aloud: order matters. `> both.txt` redirects stdout; `2>&1` sends stderr to stdout’s destination.

## 4. Pipelines

```bash
cat alpha/fruits.txt
cat alpha/fruits.txt | sort
cat alpha/fruits.txt | sort | uniq
cat alpha/fruits.txt | sort | uniq | wc -l
```

## 5. `grep` in a pipeline

```bash
cat logs/app.log
cat logs/app.log | grep error
cat logs/app.log | grep error | wc -l
```

## 6. Expansion preview

```bash
echo *
echo alpha/*.txt
echo {one,two,three}
echo file{1..5}.txt
echo "$HOME"
echo '$HOME'
```

Explain wildcard expansion, brace expansion, variable expansion, single quotes, and double quotes.

## 7. Dangerous glob habit

```bash
ls tmp/*.tmp
rm tmp/*.tmp
touch tmp/junk.tmp
```

Rule: preview wildcard matches before using `rm`.

## Cleanup

```bash
cd ~/tlcl-summer-drills
rm -f thursday.txt errors.txt both.txt
touch tmp/junk.tmp
```
