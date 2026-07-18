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

## Feynman analogy before reading

Imagine a messy pile of index cards. Each card has one line of text.

```text
cat  = show the cards
wc   = count the cards, words, or characters
sort = put cards in order
uniq = remove repeated neighboring cards
```

Important: `uniq` only notices duplicates that are next to each other. That is why `sort | uniq` is common.

## Before touching the keyboard

Answer:

1. Why does `uniq` often need `sort` before it?
2. What question does `wc -l` answer?
3. Why is `less` safer than `cat` for a huge file?
4. Does `sort file.txt` modify the file itself?

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

Say aloud:

```text
wc counts lines, words, and bytes. wc -l counts lines only.
```

### Drill 2: Sort without changing the file

```bash
sort repeated.txt
cat repeated.txt
```

Question: Did `sort` modify the file?

Expected: No. It sorted the output stream only.

### Drill 3: Understand `uniq`

```bash
uniq repeated.txt
sort repeated.txt | uniq
sort repeated.txt | uniq -c
```

Say aloud:

```text
uniq removes adjacent duplicate lines. sort makes duplicates adjacent. uniq -c counts them.
```

### Drill 4: Build one stage at a time

```bash
cat repeated.txt
cat repeated.txt | sort
cat repeated.txt | sort | uniq
cat repeated.txt | sort | uniq | wc -l
```

Question: What question does the final command answer?

Expected: How many unique lines are in `repeated.txt`?

### Drill 5: Explain to a ten-year-old

Explain:

```bash
sort repeated.txt | uniq -c
```

Use:

```text
First it...
Then it...
The final output tells me...
```

## Checkpoint

He understands Unit 1 only if he can answer:

1. Why does `uniq` often follow `sort`?
2. What is the difference between `wc` and `wc -l`?
3. Does `sort file.txt` modify the file?
4. What does `sort file.txt | uniq -c` show?
5. How do you count unique lines?
