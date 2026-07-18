#standard-IO #pipeline 
#Book/The-Linux-Command-Line  #Author/Shotts 

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
# Session 4: `tee` and Pipeline Debugging

## Read these Chapter 6 sections

Read the section covering:

```text
tee
```

Also review examples where pipelines are built from filters.

---

## Feynman analogy

A pipe normally sends output forward.

`tee` is like a T-shaped pipe fitting:

```text
one copy continues through the pipeline
one copy is saved to a file
```

This is useful when you want to inspect or save an intermediate stage.

---

## Before touching the keyboard

Answer:

1. Why would you want to save intermediate output?
2. How is `tee` different from `>`?
3. Does `tee` stop the pipeline?
4. Why is `tee` useful for debugging?

---

## Practice 1: Save and continue

```bash
cd ~/tlcl-ch06-pipeline-lab

grep ERROR input/app.log | tee output/error-lines.txt | wc -l
cat output/error-lines.txt
```

Explain:

```text
grep selected ERROR lines.
tee saved those lines to a file.
wc -l counted the same stream.
```

---

## Practice 2: Debug a pipeline with `tee`

Without `tee`:

```bash
grep ERROR input/app.log | cut -d ' ' -f2 | sort | uniq -c
```

With `tee`:

```bash
grep ERROR input/app.log \
  | tee output/stage1-error-lines.txt \
  | cut -d ' ' -f2 \
  | tee output/stage2-hosts.txt \
  | sort \
  | uniq -c
```

Inspect:

```bash
cat output/stage1-error-lines.txt
cat output/stage2-hosts.txt
```

Question:

```text
Did each stage contain what you expected?
```

---

## Practice 3: Compare `tee` with redirection

Redirection ends the stream into a file:

```bash
grep ERROR input/app.log > output/errors-only.txt
```

`tee` saves and continues:

```bash
grep ERROR input/app.log | tee output/errors-tee.txt | wc -l
```

Explain:

```text
> sends stdout to a file.
tee writes to a file and also passes stdout onward.
```

---

## Pipeline debugging checklist

When a pipeline gives a surprising answer, do not guess.

Use this:

```text
1. Run stage 1 only.
2. Count or inspect stage 1.
3. Add stage 2.
4. Inspect stage 2.
5. Continue one stage at a time.
6. Use tee if you need to save intermediate output.
7. State the assumption each stage makes.
```

---

## Broken pipeline drill

Run this:

```bash
grep ERROR input/app.log | cut -d ':' -f2 | sort | uniq -c
```

Question:

```text
Why is the output not useful?
```

Expected idea:

```text
The log lines are space-separated, not colon-separated. The cut delimiter is wrong.
```

Debug:

```bash
grep ERROR input/app.log

grep ERROR input/app.log | cut -d ':' -f2

grep ERROR input/app.log | cut -d ' ' -f2
```

Explain:

```text
The problem was not grep, sort, or uniq. The wrong assumption was the delimiter used by cut.
```

---

## Explain-back drill

Explain:

```bash
grep ERROR input/app.log | tee output/errors.txt | wc -l
```

Use:

```text
The grep stage...
The tee stage...
The wc stage...
The saved file contains...
The final output answers...
```

---

## End standard

He understands Session 4 only if he can say:

```text
tee helps me save and inspect a stream without stopping the pipeline.
When a pipeline is wrong, I debug one stage at a time instead of randomly changing commands.
```
