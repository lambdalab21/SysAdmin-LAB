# The Linux Command Line, Chapter 6: Redirection and Pipeline Thinking

This guide is for Shotts, *The Linux Command Line*, Chapter 6.

A cleaner phrasing: “Please create Markdown file(s) for Chapter 6, using disciplined thinking.”

Chapter 6 is not just about typing symbols like `>`, `>>`, and `|`.

It trains this mental model:

```text
commands produce streams
streams can be redirected
streams can be connected
connected commands become pipelines
```

Core discipline:

```text
Do not type a pipeline in one shot.
Build it one stage at a time.
Inspect each stage.
Then add the next stage.
```

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

Before every command, answer:

```text
1. What stream will this command produce?
2. Where will stdout go?
3. Where will stderr go?
4. Am I overwriting a file or appending?
5. If using a pipeline, have I inspected the previous stage?
```

---
# Session 5: Applied Lab and Self-Test

## Purpose

This session checks whether Chapter 6 became a thinking tool.

Do not use notes at first.

For every task:

```text
Write the English question.
Predict the stream.
Build one stage.
Inspect.
Add the next stage.
Explain the final answer.
```

---

# Lab 1: Fruit summary

Question:

```text
Which fruit appears most often?
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
What does each stage do?
Why does uniq need sorted input?
```

---

# Lab 2: Error count

Question:

```text
How many ERROR lines are in the log?
```

Expected command:

```bash
grep ERROR input/app.log | wc -l
```

Explain:

```text
grep answers which lines match.
wc -l answers how many lines.
```

---

# Lab 3: Error host summary

Question:

```text
Which hosts produced ERROR lines, and how many for each?
```

Expected command:

```bash
grep ERROR input/app.log | cut -d ' ' -f2 | sort | uniq -c
```

Required explanation:

```text
Stage 1 selects...
Stage 2 extracts...
Stage 3 groups...
Stage 4 counts...
```

---

# Lab 4: Save a report

Question:

```text
Create a small report file showing error lines and error count.
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
Which parts are fixed text?
Which parts come from command output?
Why did > go at the end of the command group?
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
What went to stdout?
What went to stderr?
Why are they separate?
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
What did I expect?
What did I get?
Which stage first went wrong?
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
2. Append the current date to `output/input-list.txt`.
3. Redirect only errors from a bad `ls` command to `output/errors.txt`.
4. Count lines in `input/fruits.txt`.
5. Sort `input/fruits.txt`.
6. Count unique fruit names.
7. Count ERROR lines in `input/app.log`.
8. Save ERROR lines to a file while also counting them.
9. Send unwanted errors to `/dev/null`.
10. Explain the difference between `>` and `>>`.

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

---

# Final Feynman explanation

Explain this to a younger student:

```bash
grep ERROR input/app.log | cut -d ' ' -f2 | sort | uniq -c | sort -n
```

Use simple language:

```text
grep keeps...
cut extracts...
sort groups...
uniq -c counts...
sort -n orders...
The final answer tells me...
One assumption this pipeline makes is...
```

---

# End standard

He understands Chapter 6 only if he can say:

```text
I know the difference between stdout and stderr.
I know when I am overwriting or appending.
I can build pipelines one stage at a time.
I can use tee to inspect intermediate output.
I can explain what question a pipeline answers.
I do not type long pipelines mindlessly.
```
