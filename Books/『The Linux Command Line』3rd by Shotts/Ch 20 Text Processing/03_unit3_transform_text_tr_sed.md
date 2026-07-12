#Book/The-Linux-Command-Line #Author/Shotts 
#text-processing
# The Linux Command Line, Chapter 20: Text Processing

Use these files as a one-week plan. Each day has a reading target, a Feynman-style analogy, questions, and command practice.

Core habit:

```text
Do not memorize commands as isolated tricks. Use each command to answer a clear question about text.
```

Disciplined thinking pattern:

```text
1. What question am I trying to answer?
2. What text do I have?
3. What part of the text matters?
4. What command transforms or filters it?
5. How can I inspect the result before trusting it?
```

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

## Feynman analogy before reading

Text is moving on a conveyor belt.

`tr` is a simple machine that changes characters one by one.

`sed` is a smarter machine that reads one line at a time and applies editing rules.

```text
tr  = character transformer
sed = stream editor for lines and patterns
```

## Before touching the keyboard

Answer:

1. Why is `tr` good for character-level changes?
2. Why is `sed` better for replacing words or patterns?
3. What does global replacement mean?
4. Why should you test `sed` output before editing real files?

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

Say aloud:

```text
-d deletes characters. -s squeezes repeated characters.
```

### Drill 3: Basic `sed` substitution

```bash
sed 's/ERROR/FAIL/' sample.log
sed 's/app01/web01/' sample.log
cat sample.log
```

Question: Did `sed` modify `sample.log`?

Expected: No. It printed transformed output.

### Drill 4: Global substitution

```bash
printf 'red red blue
red green red
' > colors.txt
sed 's/red/RED/' colors.txt
sed 's/red/RED/g' colors.txt
```

Say aloud:

```text
Without g, sed replaces only the first match on each line. With g, it replaces all matches.
```

### Drill 5: Print selected lines

```bash
sed -n '1,3p' sample.log
sed -n '/ERROR/p' sample.log
sed -n '/failed-login/p' sample.log
```

Say aloud:

```text
-n suppresses automatic printing. p prints selected lines.
```

### Drill 6: Delete matching lines from output

```bash
sed '/INFO/d' sample.log
sed '/ERROR/d' sample.log
```

### Drill 7: Explain to a ten-year-old

Explain:

```bash
sed -n '/ERROR/p' sample.log
```

Use:

```text
The file is like a stack of cards. sed checks one card at a time and only shows cards that contain...
```

## Checkpoint

1. What is the difference between `tr` and `sed`?
2. What does `tr -d` do?
3. What does `tr -s` do?
4. What is the difference between `s/red/RED/` and `s/red/RED/g`?
5. What does `sed -n '/ERROR/p' file` do?
