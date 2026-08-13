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

## After reading: concept questions

Answer without looking back:

1. What does `grep -i` do? case-insensitive search. 
2. What do `-A`, `-B`, and `-C` mean? -A = lines after match; =B  = lines before;  -C = lines  before and after. 
3. Why are line numbers useful? They show the exact location of the match. 
4. Why are filenames useful when searching many files? They show which file the match came from. 
5. What does `-R` do? It performs recursive searches through directories. 
6. What does `-v` do? It shows non-matching lines. 
7. Why can `grep -v` be dangerous if used without thinking? It can hide important lines that are still needed. 

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

1. Did it find uppercase and lowercase errors? Yes. 
2. Did it also find `stderr`? Yes. 
3. Is that a false positive for “application error events”? Yes. 
4. How could you make the search stricter? Use word boundaries or stricter patterns. 

Try:

```bash
grep -E ' ERROR | error ' app.log
```

Then explain whether this is better or still imperfect.

---

# Exercise 2: ABC context

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

1. Which command answered “what happened after?” -A 2
2. Which answered “what led up to this?” -B 2
3. Which gave the best investigation context? -C 2
4. Why can context be more useful than a single matching line? Surrounding lines show cause and effect. 

---

# Exercise 3: Filenames and line numbers

## Skill being gained

Search multiple files and preserve evidence location.

## Read before exercise

Reread “Working with Multiple Files.”

## Run

```bash
grep ERROR logs/*/*.log

grep -Hn ERROR logs/*/*.log
```

## Explain-back

Answer:

1. Why is `-Hn` better for investigations? Gives exact file and lines for evidence. 
2. What information is added before the matching line? Filenames and the line numbers. 
3. How would you report the evidence to someone else? Quote "file:line:matching text"

---

# Exercise 4: Recursive search

## Run

```bash
grep -R -Hn ERROR logs
```

## Explain-back

Answer:

1. What directories did grep search? Under all logs and subdirectories. 
2. How did `-Hn` help? Showed files and lines for every match. 
3. What risk exists if you recursively grep your whole home directory? Slow, noisy, and can expose secrets or overwhelm outputs. 

---

# Exercise 5: Invert matches with `-v`

## Run

```bash
grep -v '^#' sshd_config.sample
```

## Explain-back

Answer:

1. What did `-v` remove? Comment Lines. 
2. What does the extended regex remove? (typical extended form ^(#|$) also removes blank lines. 
3. Why must `grep -v` be used carefully? It's easy to drop needed liens by accident. 