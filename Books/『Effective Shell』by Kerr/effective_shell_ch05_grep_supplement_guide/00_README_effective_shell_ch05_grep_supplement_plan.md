#Book/Effective-Shell #Author/Kerr
# Effective Shell Chapter 5: Getting to Grips with `grep`

Supplement to:

```text
TLCL Chapter 19 — Regular Expressions
TLCL Chapter 20 — Text Processing
```

Purpose:

```text
Refresh regex and text-processing memory.
Then make grep useful as a daily shell tool.
```

This is not a “type these commands” guide.

The student should learn this disciplined habit:

```text
Question
→ text source
→ pattern in English
→ expected matches
→ expected false matches
→ grep command
→ inspect output
→ improve pattern or pipeline
```

Feynman analogy:

```text
grep is like a highlighter with rules.
A sloppy highlighter marks too much.
A precise highlighter marks only what helps the investigation.
```

## How this guide is split

| File | Read first | Main gain |
|---|---|---|
| `01_day1_grep_as_line_filter_and_regex_refresh.md` | “What is Grep,” “Why Grep,” “Searching Through Text” | Remember that grep searches or filters lines of text |
| `02_day2_patterns_precision_and_false_matches.md` | “Using Patterns,” plus TLCL Ch. 19 review | Use regex deliberately and avoid false matches |
| `03_day3_context_invert_and_multifile_search.md` | “Finding Problems,” “The ABC of Grep,” “Working with Multiple Files,” “V for Invert” | Use grep for real investigations, not just toy searches |
| `04_day4_pipelines_alternatives_and_final_lab.md` | “Don’t Forget Your Pipelines,” “Alternatives to Grep,” “Summary” | Combine grep with TLCL Ch. 20 text-processing tools |
| `05_review_quiz_and_retention_drills.md` | After all of Chapter 5 | Retrieval practice and final self-test |

## Setup once

Use a disposable practice directory:

```bash
mkdir -p ~/effective-shell-ch05-grep-lab
cd ~/effective-shell-ch05-grep-lab
```

Create sample files:

```bash
cat > app.log <<'EOF'
2026-07-12T08:00:01 INFO  app01 started service=web
2026-07-12T08:00:05 WARN  app01 disk usage 82 percent
2026-07-12T08:00:08 ERROR app01 failed login user=alice source=10.0.0.5
2026-07-12T08:00:12 INFO  app02 started service=db
2026-07-12T08:00:20 error app02 connection refused port=5432
2026-07-12T08:00:25 INFO  app01 request status=200 path=/index.html
2026-07-12T08:00:26 INFO  app01 request status=404 path=/favicon.ico
2026-07-12T08:00:30 WARN  app02 memory pressure high
2026-07-12T08:00:35 ERROR app02 failed backup reason=permission-denied
2026-07-12T08:00:40 INFO  app01 stderr redirect configured
EOF

cat > sshd_config.sample <<'EOF'
#Port 22
Port 2222
#PermitRootLogin yes
PermitRootLogin no
PasswordAuthentication no
# PasswordAuthentication yes
AllowUsers admin deploy
EOF

mkdir -p logs/app01 logs/app02
cp app.log logs/app01/app01.log
sed 's/app01/app02/g' app.log > logs/app02/app02.log

cat > packages.txt <<'EOF'
bash [installed]
grep [installed]
sed [installed]
awk [installed]
ripgrep [available]
not-installed-demo
EOF
```

## Rules for every exercise

Before typing, answer:

```text
1. What question am I answering?
2. What text am I searching?
3. Am I searching literal text or a regex pattern?
4. What should match?
5. What should not match?
6. Do I need context, filenames, line numbers, or inverted matches?
```

After running, answer:

```text
1. Did the output answer the question?
2. What false positives appeared?
3. What false negatives might be hidden?
4. Would another tool be better after grep narrows the data?
```
