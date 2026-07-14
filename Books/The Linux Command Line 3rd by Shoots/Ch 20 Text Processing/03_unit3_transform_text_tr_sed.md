# The Linux Command Line, Chapter 20: Text Processing

Use these files as a one-week plan. Each day has a reading target, a Feynman-style analogy, questions, and command practice.

One-time setup:

```bash
mkdir -p ~/tlcl-ch20-week/{unit1,unit2,unit3,unit4,unit5,apply}
cd ~/tlcl-ch20-week

cat > sample.log <<'EOF'
INFO 2026-06-01 app01 started
ERROR 2026-06-01 app01 failed-login alice
INFO 2026-06-01 app01 request /index.html
WARN 2026-06-01 db01 disk-space-low
ERROR 2026-06-02 db01 timeout
ERROR 2026-06-02 app01 failed-login bob
INFO 2026-06-02 app01 stopped
EOF

cat > users.tsv <<'EOF'
alice	1001	project
bob	1002	project
charlie	1003	guest
diana	1004	admin
EOF

cat > scores.txt <<'EOF'
Alice 92
Bob 7
Charlie 81
Diana 100
EOF

cat > repeated.txt <<'EOF'
banana
apple
banana
orange
apple
apple
EOF
```

---

# Unit 3: Transforming Text with `tr` and `sed`

Suggested day: Wednesday

## Read these Chapter 20 sections

Read the sections covering:

```text
tr
sed
```

For `sed`, focus only on:

```text
s/old/new/
s/old/new/g
-n ... p
/pattern/d
```

## Before touching the keyboard

Answer:

1. Why is `tr` good for character-level changes? `tr` is good for character-level changes because it works on one character at a time, like converting letters or deleting symbols. 
2. Why is `sed` better for replacing words or patterns? `sed` is better for replacing words or patterns because it can match text inside whole lines, not just single characters.
3. What does global replacement mean? Global replacement is replacing every match on a line instead of just the first one.  
4. Why should you test `sed` output before editing real files? sed output should be tested first because it shows the changes without changing the files. 

## Practice

```bash
cd ~/tlcl-ch20-week
cp sample.log unit3/
cd unit3
cat sample.log
```

### Drill 1: `tr` uppercase and lowercase

```bash
echo 'hello world' | tr '[:lower:]' '[:upper:]'
echo 'HELLO WORLD' | tr '[:upper:]' '[:lower:]'
```

### Drill 2: `tr` delete and squeeze

```bash
echo 'abc123def456' | tr -d '[:alpha:]'
echo 'hello     world' | tr -s ' '
echo '2026-06-17' | tr '-' '/'
```

### Drill 3: Basic `sed` substitution

```bash
sed 's/ERROR/FAIL/' sample.log
sed 's/app01/web01/' sample.log
cat sample.log
```

Question: Did `sed` modify `sample.log`? No, it printed transformed output. 

### Drill 4: Global substitution

```bash
printf 'red red blue
red green red
' > colors.txt
sed 's/red/RED/' colors.txt
sed 's/red/RED/g' colors.txt
```

### Drill 5: Print selected lines

```bash
sed -n '1,3p' sample.log
sed -n '/ERROR/p' sample.log
sed -n '/failed-login/p' sample.log
```

### Drill 6: Delete matching lines from output

```bash
sed '/INFO/d' sample.log
sed '/ERROR/d' sample.log
```

## Checkpoint

1. What is the difference between `tr` and `sed`? tr changes individual characters while sed edits text by lines and patterns. 
2. What does `tr -d` do? tr -d deletes the specified characters. 
3. What does `tr -s` do? tr -s squeezes repeated characters into a single one. 
4. What is the difference between `s/red/RED/` and `s/red/RED/g`? s/red/RED/ changes only one first red on each line, while s/red/RED/g changes all red matches on each line. 
5. What does `sed -n '/ERROR/p' file` do? sed -n '/ERROR/p' file prints only the lines that contain ERROR. 