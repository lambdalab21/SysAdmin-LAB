#Book/The-Linux-Command-Line  #Author/Shotts 
#process 
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

# Unit 2: Extracting Columns and Combining Text

Suggested day: Tuesday

## Read these Chapter 20 sections

Read the sections covering:

```text
cut
paste
join
```

Skim nearby comparison tools if they appear, but focus on `cut`, `paste`, and `join`.

## Feynman analogy before reading

Imagine a spreadsheet.

```text
cut   = choose columns from each row
paste = put files side by side
join  = combine rows that share a matching key
```

`cut` is the most important command in this unit. `join` is useful but fussy because input often must be sorted.

## Before touching the keyboard

Answer:

1. What is a delimiter?
2. What delimiter does a TSV file use?
3. Why is `/etc/passwd` good practice data for `cut`?
4. Why might `join` require sorted input?

## Practice

```bash
cd ~/tlcl-ch20-week
cp users.tsv unit2/
cd unit2
cat users.tsv
```

### Drill 1: Cut fields from TSV

```bash
cut -f1 users.tsv
cut -f2 users.tsv
cut -f3 users.tsv
cut -f1,3 users.tsv
```

Say aloud:

```text
-f selects fields. With no -d option, cut expects tabs.
```

### Drill 2: Cut colon-delimited fields

```bash
cut -d ':' -f1 /etc/passwd | head
cut -d ':' -f1,3 /etc/passwd | head
cut -d ':' -f1,7 /etc/passwd | head
```

Question: What field from `/etc/passwd` usually contains the login shell?

Expected: Field 7.

### Drill 3: Paste files side by side

```bash
printf 'alice
bob
charlie
' > names.txt
printf 'admin
deploy
guest
' > roles.txt
paste names.txt roles.txt
paste -d ':' names.txt roles.txt
```

Say aloud:

```text
paste combines corresponding lines from files.
```

### Drill 4: Join on a key

```bash
cat > ids.txt <<'EOF'
1 alice
2 bob
3 charlie
EOF
cat > roles-by-id.txt <<'EOF'
1 admin
2 deploy
3 guest
EOF
join ids.txt roles-by-id.txt
```

Now create unsorted input and test:

```bash
cat > unsorted-ids.txt <<'EOF'
2 bob
1 alice
3 charlie
EOF
join unsorted-ids.txt roles-by-id.txt
```

Explain what happened.

### Drill 5: Rows then columns

Question: Which users are in the project group?

Build gradually:

```bash
cat users.tsv
grep 'project' users.tsv
grep 'project' users.tsv | cut -f1
```

Say aloud:

```text
First select rows, then select fields.
```

## Checkpoint

1. What does `cut -f1` do?
2. What does `cut -d ':' -f1` do?
3. What does `paste` do?
4. What does `join` need to work reliably?
5. How do you extract usernames from `/etc/passwd`?
