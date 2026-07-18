#Book/Effective-Shell #Author/Kerr
# Day 3: Context, Invert, and Multi-File Search

## Read before exercises

Read Effective Shell Chapter 5 sections:

```text
Finding Problems
The ABC of Grep
Working with Multiple Files
V for Invert
```

Review TLCL Chapter 20:

```text
grep as a filter
wc for counting
sort and uniq for summarizing
less for paging output
```

## What he should gain

He should gain this idea:

```text
Real grep work is often investigation.
You need context, filenames, line numbers, and sometimes inverted matches.
```

He is learning to gather evidence.

## Before reading: Feynman preview

Explain this before reading:

```text
If one log line is a clue, surrounding lines are the scene.
-A, -B, and -C let grep show the scene around the clue.
```

## After reading: concept questions

Answer without looking back:

1. What does `grep -i` do?
2. What do `-A`, `-B`, and `-C` mean?
3. Why are line numbers useful?
4. Why are filenames useful when searching many files?
5. What does `-R` do?
6. What does `-v` do?
7. Why can `grep -v` be dangerous if used without thinking?

---

# Exercise 1: Find likely problems

## Skill being gained

Use quick broad searching to locate suspicious lines.

## Do not type yet

Predict:

```text
grep -i err app.log
```

Answer:

```text
What true error lines should match?
What non-error lines might also match?
```

## Run

```bash
grep -i err app.log
```

## Explain-back

Answer:

1. Did it find uppercase and lowercase errors?
2. Did it also find `stderr`?
3. Is that a false positive for “application error events”?
4. How could you make the search stricter?

Try:

```bash
grep -E ' ERROR | error ' app.log
```

Then explain whether this is better or still imperfect.

---

# Exercise 2: ABC context

## Skill being gained

Use context lines to understand what happened around a match.

## Read before exercise

Reread Kerr’s “ABC of Grep” section.

## Predict before typing

For each command, predict the shape of output:

```text
-A 2 = match plus 2 lines after
-B 2 = 2 lines before plus match
-C 2 = 2 lines before and 2 lines after
```

## Run

```bash
grep -A 2 'failed login' app.log

grep -B 2 'failed backup' app.log

grep -C 2 'connection refused' app.log
```

## Explain-back

Answer:

1. Which command answered “what happened after?”
2. Which answered “what led up to this?”
3. Which gave the best investigation context?
4. Why can context be more useful than a single matching line?

---

# Exercise 3: Filenames and line numbers

## Skill being gained

Search multiple files and preserve evidence location.

## Read before exercise

Reread “Working with Multiple Files.”

## Predict before typing

Answer:

```text
If I search logs/*/*.log, how will I know which file had the match?
What will -H do?
What will -n do?
```

## Run

```bash
grep ERROR logs/*/*.log

grep -Hn ERROR logs/*/*.log
```

## Explain-back

Answer:

1. Why is `-Hn` better for investigations?
2. What information is added before the matching line?
3. How would you report the evidence to someone else?

Write one evidence note:

```text
File:
Line:
Problem:
Possible next check:
```

---

# Exercise 4: Recursive search

## Skill being gained

Search a directory tree.

## Predict before typing

Answer:

```text
What does recursive mean?
What files should grep search under logs?
Why might recursive search produce too much output in a large directory?
```

## Run

```bash
grep -R -Hn ERROR logs
```

## Explain-back

Answer:

1. What directories did grep search?
2. How did `-Hn` help?
3. What risk exists if you recursively grep your whole home directory?

---

# Exercise 5: Invert matches with `-v`

## Skill being gained

Use `grep -v` to remove noise from output.

## Read before exercise

Reread “V for Invert.”

## Predict before typing

Question:

```text
I want config lines that are not comments and not blank.
```

Predict the effect:

```bash
grep -v '^#' sshd_config.sample
```

What noise remains?

## Run

```bash
grep -v '^#' sshd_config.sample
```

Now create a file with blanks and test:

```bash
printf '\n# comment\nPort 22\n\nPasswordAuthentication no\n' > config-with-blanks.sample

grep -v '^#' config-with-blanks.sample

grep -Ev '^[[:space:]]*(#|$)' config-with-blanks.sample
```

## Explain-back

Answer:

1. What did `-v` remove?
2. Why did the first command leave blank lines?
3. What does the extended regex remove?
4. Why must `grep -v` be used carefully?

---

# Day 3 finish standard

He is done only if he can say:

```text
-A, -B, and -C show context.
-Hn gives file and line evidence.
-R searches recursively.
-v filters out matching lines.
I should know whether I am collecting evidence or removing noise.
```
