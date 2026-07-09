# BashGuide Patterns Reading Guide

Source: Greg's Wiki, `BashGuide/Patterns`  
URL: https://mywiki.wooledge.org/BashGuide/Patterns

Purpose: build enough glob/pattern skill to be effective in Bash, and to prevent confusing shell globs with regex.

One-time lab setup:

```bash
mkdir -p ~/bashguide-patterns-lab/{docs,logs,images,archive,tmp}
cd ~/bashguide-patterns-lab

touch docs/report.txt docs/report-old.txt docs/README.md docs/README.TXT
touch logs/app.log logs/app-2026.log logs/error.log logs/debug.tmp
touch images/tokyo.jpg images/california.bmp images/names.txt
touch archive/old-report.txt archive/old.log
touch tmp/junk.tmp tmp/'file with spaces.txt' tmp/.hidden.tmp
```

Safe preview command for all glob practice:

```bash
printf '<%s>
' PATTERN
```

Replace `PATTERN` with the glob you are testing.

---

# Part 1: Write commands from English

## 1. Preview all `.txt` files in the current directory.

Expected pattern:

```bash
printf '<%s>
' *.txt
```
## 2. Preview all `.log` files in the `logs` directory.

Expected pattern:

```bash
printf '<%s>
' logs/*.log
```

## 3. Test whether a variable contains a `.jpg` filename.

Expected pattern:

```bash
[[ $file = *.jpg ]]
```

## 4. Test whether a line begins with `ERROR` using regex.

Expected pattern:

```bash
[[ $line =~ ^ERROR ]]
```

## 5. Generate `chapter-01.md` through `chapter-05.md`.

Expected pattern:

```bash
echo chapter-{01..05}.md
```

## 6. With extglob enabled, preview files that are not `.jpg` or `.bmp`.

Expected pattern:

```bash
shopt -s extglob
printf '<%s>
' !(*.jpg|*.bmp)
```

---

# Part 2: Diagnose mistakes

## Mistake 1

```bash
for f in $(ls); do echo "$f"; done
```

Explain the problem:

```text
It parses ls output and can split filenames with spaces.
```

Better:

```bash
for f in *; do printf '<%s>
' "$f"; done
```

---

## Mistake 2

```bash
find . -name *.txt
```

Explain the problem:

```text
The shell may expand *.txt before find receives it.
```

Better:

```bash
find . -name "*.txt"
```

---

## Mistake 3

```bash
grep '*.txt' filelist.txt
```

Explain the problem:

```text
This is regex, not glob. In regex, * repeats the previous thing.
```

Better examples depending on intent:

```bash
grep '\.txt$' filelist.txt
```

or for shell filename selection:

```bash
printf '<%s>
' *.txt
```

---

## Mistake 4

```bash
rm !(*.jpg|*.bmp)
```

Explain the risk:

```text
Extended negative globs can match more than expected. Preview first.
```

Safer:

```bash
printf 'Would remove: <%s>
' !(*.jpg|*.bmp)
```