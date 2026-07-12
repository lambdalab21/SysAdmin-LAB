#Book/The-Linux-Command-Line Author/Shotts 
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
# Session 1: Streams — stdin, stdout, stderr

## Read these Chapter 6 sections

Read the opening of Chapter 6 and the sections introducing:

```text
standard input
standard output
standard error
file descriptors
```

Focus on the idea before the symbols.

---

## Feynman analogy

Imagine a machine in a workshop.

```text
stdin  = the input tray
stdout = the normal output tray
stderr = the warning/error tray
```

A command can produce useful output and error messages at the same time. A disciplined shell user keeps those streams separate in his mind.

---

## Before touching the keyboard

Answer:

1. What is stdout?
2. What is stderr?
3. What is stdin?
4. Why are errors not the same as normal output?
5. Why does this matter for pipelines?

---

## Practice 1: Normal stdout

```bash
cd ~/tlcl-ch06-pipeline-lab
ls
cat input/fruits.txt
wc -l input/fruits.txt
```

Explain:

```text
The normal result appeared on the screen because stdout went to the terminal.
```

---

## Practice 2: Create stderr on purpose

Run:

```bash
ls input/fruits.txt
ls input/missing-file.txt
```

Question:

```text
Which command produced normal output?
Which command produced an error message?
```

Now run:

```bash
ls input/fruits.txt input/missing-file.txt
```

Explain:

```text
One pathname exists, so it produced stdout.
One pathname is missing, so it produced stderr.
Both appeared on the screen, but they are different streams.
```

---

## Practice 3: Feynman explain-back

Explain this command to a younger student:

```bash
ls input/fruits.txt input/missing-file.txt
```

Use:

```text
The command checks two paths.
The existing path creates...
The missing path creates...
The reason both appear on the screen is...
```

---

## Practice 4: stdin

Run:

```bash
sort
```

Type:

```text
banana
apple
orange
```

Then press `Ctrl-d`.

Question:

```text
Why did sort wait for input?
```

Expected idea:

```text
Without a file argument, sort reads from stdin.
Ctrl-d tells the shell there is no more input.
```

Now compare:

```bash
sort input/fruits.txt
```

---

## Discipline checkpoint

Fill this out:

```text
Command:
What does it read?
What does it write to stdout?
What might it write to stderr?
```

Use it for:

```bash
cat input/fruits.txt
sort input/fruits.txt
ls input/fruits.txt input/missing-file.txt
wc -l input/fruits.txt
```

---

## End standard

He understands Session 1 only if he can say:

```text
stdout is normal output.
stderr is error output.
stdin is input.
A pipeline usually connects stdout from one command to stdin of another.
```
