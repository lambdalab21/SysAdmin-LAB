#Book/The-Linux-Command-Line #Author/Shotts 
#redirection
# The Linux Command Line, Chapter 6: Redirection and Pipeline Thinking

This guide is for Author/Shotts, *The Linux Command Line*, Chapter 6.

One-time setup:

```bash
mkdir -p ~/tlcl-ch06-pipeline-lab/{input,output,tmp}
cd ~/tlcl-ch06-pipeline-lab

cat > input/fruits.txt <<'EOF'
banana
apple
orange
banana
apple
apple
grape
orange
EOF

cat > input/app.log <<'EOF'
INFO app01 started
ERROR app01 failed-login alice
INFO app01 request /index.html
WARN db01 disk-space-low
ERROR db01 timeout
ERROR app01 failed-login bob
INFO app01 stopped
EOF

cat > input/users.txt <<'EOF'
alice project
bob project
charlie guest
diana admin
EOF
```

---
# Lab 1: Fruit summary

Question:

```text
Which fruit appears most often? Apple
```

Build:

```bash
cat input/fruits.txt
sort input/fruits.txt
sort input/fruits.txt | uniq -c
sort input/fruits.txt | uniq -c | sort -n
```

Explain:

```text
What does each stage do? Cat shows the file. Sort lists lines out alphabetically, and sort | uniq -c: counts occurrences of each unique line. 
Why does uniq need sorted input?
```

---

# Lab 2: Error count

Question:

```text
How many ERROR lines are in the log? Three. 
```

Expected command:

```bash
grep ERROR input/app.log | wc -l
```

---

# Lab 3: Error host summary

Question:

```text
Which hosts produced ERROR lines, and how many for each? 2 app01, 1 dbo01.
```

Expected command:

```bash
grep ERROR input/app.log | cut -d ' ' -f2 | sort | uniq -c
```

---

# Lab 4: Save a report

Question:

```text
Create a small report file showing error lines and error count.
(This isn't a question.)
```

Command:

```bash
{
  echo 'Error report'
  echo '============'
  echo
  echo 'Error lines:'
  grep ERROR input/app.log
  echo
  printf 'Error count: '
  grep ERROR input/app.log | wc -l
} > output/error-report.txt

cat output/error-report.txt
```

Explain:

```text
Which parts are fixed text? The headings and labels.("Error report", "Error lines", "Error count")
Which parts come from command output? The error lines and the number from grep + wc. 
Why did > go at the end of the command group? It redirects the entire command group {...} output to the file. 
```

---

# Lab 5: Separate good and bad output

Run:

```bash
ls input/fruits.txt input/missing.txt > output/found.txt 2> output/not-found.txt
```

Inspect:

```bash
cat output/found.txt
cat output/not-found.txt
```

Explain:

```text
What went to stdout? `input/fruits.txt` 
What went to stderr? Error about `input/missing.txt`
Why are they separate? catches normal  output; 2> catches errors. 
```

---

# Lab 6: Debug the wrong delimiter

Wrong command:

```bash
grep ERROR input/app.log | cut -d ':' -f2 | sort | uniq -c
```

Do not fix it immediately.

Answer:

```text
What did I expect? Host names with error counts. 
What did I get? Empty or wrong output without any matched fields. 
Which stage first went wrong? The `cut -d ':'` stage. 
```

Debug:

```bash
grep ERROR input/app.log

grep ERROR input/app.log | cut -d ':' -f2

grep ERROR input/app.log | cut -d ' ' -f2
```

Correct command:

```bash
grep ERROR input/app.log | cut -d ' ' -f2 | sort | uniq -c
```

---

# Final self-test

Without notes, write commands for each task.

1. Save the output of `ls input` to `output/input-list.txt`. 
		ls input > output/input-list.txt
2. Append the current date to `output/input-list.txt`.
		date >> output/input-list.txt
3. Redirect only errors from a bad `ls` command to `output/errors.txt`.
		ls input/missing.txt 2> output/errors.txt
4. Count lines in `input/fruits.txt`.
		wc -l input/fruits.txt
5. Sort `input/fruits.txt`.
		sort input/fruits.txt
6. Count unique fruit names.
		sort input/fruits.txt | uniq -c
7. Count ERROR lines in `input/app.log`.
		grep ERROR input/app.log | wc -l
8. Save ERROR lines to a file while also counting them.
		grep ERROR input/app.log | tee output/error-lines.txt | wc -l.
9. Send unwanted errors to `/dev/null`.
		ls input/fruits.txt input/missing.txt 2> /dev/null.
10. Explain the difference between `>` and `>>`
		> overwrites a file; >> appends to it. 

Expected command patterns:

```bash
ls input > output/input-list.txt
date >> output/input-list.txt
ls input/missing.txt 2> output/errors.txt
wc -l input/fruits.txt
sort input/fruits.txt
sort input/fruits.txt | uniq -c
grep ERROR input/app.log | wc -l
grep ERROR input/app.log | tee output/error-lines.txt | wc -l
ls input/fruits.txt input/missing.txt 2> /dev/null
```