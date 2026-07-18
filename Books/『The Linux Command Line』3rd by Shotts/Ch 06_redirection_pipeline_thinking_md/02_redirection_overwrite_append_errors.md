#Book/The-Linux-Command-Line #Author/Shotts 
#redirection
# The Linux Command Line, Chapter 6: Redirection and Pipeline Thinking

This guide is for Author/Shotts, *The Linux Command Line*, Chapter 6.

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
# Session 2: Redirection — Overwrite, Append, and Errors

## Read these Chapter 6 sections

Read the sections covering:

```text
>
>>
2>
2>>
&>
/dev/null
```

If your edition uses slightly different examples, focus on the same concepts.

---

## Feynman analogy

Normally, command output goes to the screen.

Redirection says:

```text
Do not send this stream to the screen.
Send it to this file instead.
```

Important difference:

```text
>  overwrites
>> appends
```

Overwriting is like replacing the notebook. Appending is like writing a new line at the end.

---

## Before touching the keyboard

Answer:

1. What does `>` do?
2. What does `>>` do?
3. Why is `>` dangerous?
4. What does `2>` redirect?
5. Why is `/dev/null` called a bit bucket?

---

## Practice 1: Redirect stdout with overwrite

```bash
cd ~/tlcl-ch06-pipeline-lab

ls input > output/listing.txt
cat output/listing.txt
```

Now overwrite it:

```bash
echo 'new content' > output/listing.txt
cat output/listing.txt
```

Explain:

```text
> replaced the previous contents of the file.
```

---

## Practice 2: Append stdout

```bash
echo 'first line' > output/append-test.txt
echo 'second line' >> output/append-test.txt
echo 'third line' >> output/append-test.txt
cat output/append-test.txt
```

Explain:

```text
> started or replaced the file.
>> added new content to the end.
```

---

## Practice 3: Redirect stderr

Run this first:

```bash
ls input/fruits.txt input/missing-file.txt
```

Now redirect only stderr:

```bash
ls input/fruits.txt input/missing-file.txt 2> output/errors.txt
cat output/errors.txt
```

Question:

```text
Why did the existing file still print on screen?
```

Expected idea:

```text
Only stderr was redirected. stdout still went to the terminal.
```

---

## Practice 4: Separate stdout and stderr

```bash
ls input/fruits.txt input/missing-file.txt > output/stdout.txt 2> output/stderr.txt
cat output/stdout.txt
cat output/stderr.txt
```

Explain:

```text
stdout went to stdout.txt.
stderr went to stderr.txt.
```

---

## Practice 5: Discard unwanted output

```bash
ls input/fruits.txt input/missing-file.txt 2> /dev/null
```

Question:

```text
What disappeared? What remained?
```

Expected idea:

```text
The error disappeared because stderr was sent to /dev/null. Normal output remained.
```

---

## Discipline warning

Before using `>`, say:

```text
This may overwrite the file.
Do I care about the old contents?
Should I use >> instead?
Should I inspect the file first?
```

Inspect first:

```bash
ls -l output/listing.txt
cat output/listing.txt
```

---

## Explain-back drill

Explain this command:

```bash
ls input/fruits.txt input/missing-file.txt > output/good.txt 2> output/bad.txt
```

Use:

```text
The command checks...
stdout goes to...
stderr goes to...
The screen shows...
```

---

## End standard

He understands Session 2 only if he can answer:

1. What is the difference between `>` and `>>`?
2. What does `2>` redirect?
3. Why does redirecting stderr not redirect stdout?
4. What is `/dev/null` useful for?
5. Why should he pause before pressing Enter with `>`?
