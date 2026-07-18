#Book/The-Linux-Command-Line  Author/Shotts 
#process 
# The Linux Command Line, Chapter 20: Text Processing

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

# Unit 1: Viewing, Sorting, Counting, and Duplicate Removal

Suggested day: Monday

## Read these Chapter 20 sections

Read the sections covering:

```text
cat
sort
uniq
wc
```

Also review earlier familiar commands if needed:

```text
head
tail
less
```

## Before touching the keyboard

Answer:

1. Why does `uniq` often need `sort` before it? Uniq only removes consecutive duplicates.
2. What question does `wc -l` answer? Answers how many lines there are. 
3. Why is `less` safer than `cat` for a huge file? less paginates safely, it doesn't dump everything at once. 
4. Does `sort file.txt` modify the file itself? No. Sort outputs to stdout. The original file is left unchanged. 

## Practice

```bash
cd ~/tlcl-ch20-week
cp repeated.txt unit1/
cd unit1
cat repeated.txt
```

### Drill 1: Count before transforming

Predict first, then run:

```bash
wc repeated.txt
wc -l repeated.txt
wc -w repeated.txt
wc -c repeated.txt
```

### Drill 2: Sort without changing the file

```bash
sort repeated.txt
cat repeated.txt
```

Question: Did `sort` modify the file? No. It sorted the output stream only.

### Drill 3: Understand `uniq`

```bash
uniq repeated.txt
sort repeated.txt | uniq
sort repeated.txt | uniq -c
```

### Drill 4: Build one stage at a time

```bash
cat repeated.txt
cat repeated.txt | sort
cat repeated.txt | sort | uniq
cat repeated.txt | sort | uniq | wc -l
```

Question: What question does the final command answer? The final command answers how many unique items there are. 

