#Book/The-Linux-Command-Line  #Author/Shotts 
#process 
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

## Read these Chapter 20 sections

Read the sections covering:

```text
nl
fold
fmt
pr
printf
```

Spend the most attention on:

```text
printf
nl
```

## Before touching the keyboard

Answer:

1. Why is aligned output easier to read? Aligned output is easier to read because columns line up and patterns are easier to see. 
2. Why is `printf` more controlled than `echo`? printf is more controlled than echo because you choose the same format of the output. 
3. Which command here seems most useful for scripting? printf seems most useful for scripting because it gives predictable and formatted outputs.

## Practice

```bash
cd ~/tlcl-ch20-week
cp scores.txt unit4/
cd unit4
cat > paragraph.txt <<'EOF'
Linux text processing tools are useful because they allow a student to inspect, filter, transform, and summarize data without opening a spreadsheet or writing a full program.
EOF
```

### Drill 1: Number lines with `nl`

```bash
nl scores.txt
nl -ba scores.txt
```

### Drill 2: Wrap lines with `fold`

```bash
fold -w 40 paragraph.txt
fold -w 20 paragraph.txt
```

### Drill 3: Reformat paragraph text with `fmt`

```bash
fmt -w 40 paragraph.txt
fmt -w 60 paragraph.txt
```

### Drill 4: Recognize `pr`

```bash
pr scores.txt | head
```

### Drill 5: Basic `printf`

```bash
printf 'Name: %s
' Alice
printf 'Score: %d
' 92
printf '%s scored %d
' Alice 92
```

### Drill 6: Aligned columns

```bash
printf '%-10s %5s
' Name Score
printf '%-10s %5d
' Alice 92
printf '%-10s %5d
' Bob 7
printf '%-10s %5d
' Diana 100
```

## Checkpoint

1. What does `nl` do? nl numbers the lines in a file. 
2. What does `fold -w 40` do? fold -w 40 wraps lines so that each line is at most 40 characters wide. 
3. What does `fmt -w 40` do? fmt -w 40 reformats paragraphs so that lines are wrapped neatly at about 40 characters.  
4. Why is `printf` useful? printf is useful because it can create nicely formatted, aligned outputs. 
5. What do `%s`, `%d`, and ` mean? %s means string, %d means integer number, and \n means new line. 
