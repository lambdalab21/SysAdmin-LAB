#Book/Effective-Shell #Author/Kerr
A cleaner phrasing is: “Please create drills for Chapter 2 of _Effective Shell_ by Kerr.”

Chapter 2, **“Thinking in Pipelines,”** covers `stdin`, `stdout`, `stderr`, file descriptors `0`, `1`, and `2`, pipelines, redirection, `less`, `/dev/null`, redirection order, and `tee`. Kerr specifically says these concepts are fundamental and will recur throughout the book. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

Use `playground01`. Each set should take about 2–5 minutes. Rotate the sets instead of completing all of them every day.

# One-time setup

```bash
mkdir -p ~/effective-shell-ch02
cd ~/effective-shell-ch02

cat > names.txt <<'EOF'
Lisa
Bart
Homer
Marge
Bart
Maggie
Homer
Lisa
EOF

cat > data.txt <<'EOF'
# sample data
banana
apple

orange
banana
apple
EOF
```

---

# Drill Set A: `stdin` and `Ctrl-d`

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

Then press:

```text
Ctrl-d
```

Repeat once, but press:

```text
Ctrl-c
```

instead.

Say aloud:

```text
Ctrl-d = finish entering input
Ctrl-c = interrupt the command
```

Kerr distinguishes end-of-input with `Ctrl-d` from interruption with `Ctrl-c`. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set B: Identify the standard streams

Run:

```bash
ls -l /dev/std*
```

Then say aloud:

```text
stdin  = 0
stdout = 1
stderr = 2
```

Repeat from memory without looking at the output.

---

# Drill Set C: Build a pipeline one stage at a time

Run each command separately:

```bash
cat names.txt
```

```bash
cat names.txt | sort
```

```bash
cat names.txt | sort | uniq
```

```bash
cat names.txt | sort | uniq | wc -l
```

Before adding each stage, predict the output.

Say aloud:

```text
| connects stdout on the left
  to stdin on the right
```

A pipeline is a connected sequence of programs where the pipe operator attaches one command’s `stdout` to the next command’s `stdin`. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set D: Avoid unnecessary `cat`

Run both commands:

```bash
cat names.txt | wc -l
```

```bash
wc -l names.txt
```

Run both:

```bash
cat names.txt | sort
```

```bash
sort names.txt
```

Say aloud:

```text
Use cat when it clarifies a pipeline.
Skip cat when the command can read the file directly.
```

Kerr notes that many commands accept a filename directly, so `cat` is often unnecessary. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set E: Redirect `stdout`

Run:

```bash
echo "first line" > output.txt
cat output.txt
```

Then:

```bash
echo "replacement line" > output.txt
cat output.txt
```

Then:

```bash
echo "appended line" >> output.txt
cat output.txt
```

Say aloud:

```text
>  overwrite
>> append
```

---

# Drill Set F: Redirect `stdin`

Run:

```bash
sort < names.txt
```

Then:

```bash
wc -l < names.txt
```

Compare:

```bash
wc -l names.txt
```

Say aloud:

```text
< sends a file into stdin
```

---

# Drill Set G: Use a pager

Run:

```bash
ls /usr/bin | less
```

Practice inside `less`:

```text
d       move down
u       move up
/bin    search forward
?bash   search backward
q       quit
```

Repeat with:

```bash
ps aux | less
```

Kerr recommends piping long output into `less`; inside the pager, `d`, `u`, `/`, `?`, and `q` are useful controls. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set H: Observe `stderr`

Create a directory:

```bash
mkdir testdir
```

Try to create it again:

```bash
mkdir testdir
```

Now run:

```bash
mkdir testdir | tr '[:lower:]' '[:upper:]'
```

Observe that the error message is not converted to uppercase.

Say aloud:

```text
A pipe normally carries stdout only.
Errors usually go through stderr.
```

Kerr uses this experiment to show why ordinary pipes do not automatically process error messages. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set I: Redirect `stderr` into `stdout`

Run:

```bash
mkdir testdir 2>&1 | tr '[:lower:]' '[:upper:]'
```

The error message should become uppercase.

Say aloud:

```text
2>&1
2     = stderr
>     = redirect
&1    = file descriptor 1, stdout
```

Repeat twice from memory.

---

# Drill Set J: Redirect errors to a file

Run:

```bash
rm -f errors.txt
mkdir testdir 2> errors.txt
cat errors.txt
```

Append another error:

```bash
mkdir testdir 2>> errors.txt
cat errors.txt
```

Say aloud:

```text
2>  overwrite error file
2>> append to error file
```

---

# Drill Set K: Send errors to `/dev/null`

Run:

```bash
mkdir testdir 2>/dev/null
echo "shell still running"
```

Then run:

```bash
cat missing-file.txt 2>/dev/null
```

Say aloud:

```text
/dev/null discards output.
Do not hide errors unless I understand what I am hiding.
```

Kerr warns that discarding `stderr` can also hide an error that matters. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set L: Remember the ampersand in `2>&1`

Remove any old file named `1`:

```bash
rm -f 1
```

Run the intentionally incorrect command:

```bash
cat missing-file.txt 2>1
```

Inspect:

```bash
ls -l 1
cat 1
```

Clean up:

```bash
rm 1
```

Now run the correct form:

```bash
cat missing-file.txt 2>&1
```

Say aloud:

```text
2>1   writes errors to a file named 1
2>&1  sends stderr to stdout
```

This is one of the easy-to-forget details worth practicing repeatedly. Kerr uses the same contrast to explain why `&` matters. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set M: Practice redirection order

Run the incorrect order:

```bash
ls /usr/bin /nothing 2>&1 > all-output.txt
```

Observe that the error appears on the terminal.

Inspect:

```bash
grep nothing all-output.txt
```

Now run the correct order:

```bash
ls /usr/bin /nothing > all-output.txt 2>&1
```

Inspect:

```bash
grep nothing all-output.txt
```

Say aloud:

```text
Redirections are processed from left to right.

> file 2>&1
means:
send stdout to file
then send stderr to the same destination
```

Kerr emphasizes that `ls /usr/bin /nothing > all-output.txt 2>&1` captures both streams, while reversing the order does not. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set N: Use `tee`

Run:

```bash
cat names.txt | sort | tee sorted.txt | uniq
```

Inspect:

```bash
cat sorted.txt
```

Then run:

```bash
cat names.txt | sort | uniq | tee unique.txt | wc -l
```

Inspect:

```bash
cat unique.txt
```

Say aloud:

```text
tee copies the stream to a file
and also passes it onward
```

Kerr compares `tee` to a T-shaped plumbing pipe because it sends one stream in two directions. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set O: Build a quick answer incrementally

Run:

```bash
cat data.txt
```

Then:

```bash
cat data.txt | sort
```

Then:

```bash
cat data.txt | sort | uniq
```

Then:

```bash
cat data.txt | sort | uniq | grep -v '^#'
```

Then:

```bash
cat data.txt | sort | uniq | grep -v '^#' | grep -v '^$'
```

Finally:

```bash
cat data.txt | sort | uniq | grep -v '^#' | grep -v '^$' | wc -l
```

Say aloud:

```text
Build pipelines one stage at a time.
Inspect intermediate output before adding the next stage.
```

Kerr demonstrates this incremental approach with a similar `sort | uniq | grep | wc` pipeline. ([effective-shell.com](https://effective-shell.com/part-2-core-skills/thinking-in-pipelines/ "Thinking in Pipelines | Effective Shell"))

---

# Drill Set P: Predict before running

For each command, predict:

```bash
echo "hello" > file.txt
```

```bash
echo "world" >> file.txt
```

```bash
cat file.txt | wc -l
```

```bash
cat missing.txt 2>/dev/null
```

```bash
cat missing.txt 2>&1 | tr '[:lower:]' '[:upper:]'
```

```bash
printf 'b\na\nb\n' | sort | uniq
```

Run each command only after stating the expected result.

---

# Two-minute rapid drill

Use this when there is little time:

```bash
cd ~/effective-shell-ch02

cat names.txt | sort | uniq | wc -l

echo "alpha" > quick.txt
echo "beta" >> quick.txt
cat quick.txt

cat missing.txt 2> errors.txt
cat errors.txt

cat missing.txt 2>&1 | tr '[:lower:]' '[:upper:]'

cat names.txt | sort | tee sorted.txt | uniq
```

Clean up:

```bash
rm -f quick.txt errors.txt sorted.txt all-output.txt
```

---

# Five-minute daily rotation

|Day|Drill sets|
|---|---|
|Monday|A, C, D|
|Tuesday|E, F, G|
|Wednesday|H, I, J|
|Thursday|K, L|
|Friday|M, N|
|Saturday|O|
|Sunday|Two-minute rapid drill|

---

# Commands and syntax to retain

## Use frequently

```text
|
>
>>
<
less
sort
uniq
wc
grep
```

## Practice deliberately because they are easy to forget

```text
Ctrl-d
2>
2>>
2>&1
2>/dev/null
> file 2>&1
tee
```

The most important habit is:

```text
start with one command
→ inspect its output
→ add one pipeline stage
→ inspect again
→ add the next stage only when needed
```

Do not try to write complicated pipelines in one attempt. That defeats the point of thinking in pipelines.


[reference](https://chatgpt.com/s/t_6a25ea766adc8191849658b6d33d4754)