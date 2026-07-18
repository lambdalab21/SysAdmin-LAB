#Book/Effective-Shell #Author/Kerr
#README 
# Effective Shell Ch. 6 Supplement: Slice and Dice Text

Source chapter: Dave Kerr, *Effective Shell*, Chapter 6, **Slice and Dice Text**  
Source URL: https://effective-shell.com/part-3-manipulating-text/slice-and-dice-text/

This package is a supplement to Shotts/TLCL, especially:

- TLCL Ch. 19: Regular Expressions
- TLCL Ch. 20: Text Processing

It is not meant to replace those chapters. It is meant to make the student faster and more disciplined when using ordinary text tools.

## Big goal

```text
Given a messy text stream, he should be able to:
1. inspect it safely,
2. remove irrelevant lines,
3. extract useful fields or characters,
4. transform simple characters,
5. sort and remove duplicates,
6. verify each pipeline stage before adding the next stage.
```

## Main tools in this supplement

```text
head     preview the beginning
 tail    preview the end or follow changing files
 tr      translate/delete characters
 cut     extract fields or character ranges
 rev     reverse text; useful with cut in some edge cases
 sort    order lines
 uniq    remove adjacent duplicates
 less    inspect large output safely
```

## Connection to TLCL

TLCL Ch. 20 teaches text-processing commands. Kerr Ch. 6 makes those commands feel like practical workflow tools.

The key difference:

```text
TLCL teaches: what the tools do.
Kerr reinforces: how to combine them quickly and safely.
```

## Suggested split

| Day | File | Kerr sections | Main gain |
|---|---|---|---|
| Day 1 | `01_day1_head_tail_and_safe_inspection.md` | Heads and Tails | Inspect before transforming |
| Day 2 | `02_day2_tr_cut_and_field_extraction.md` | Replacing Text; How to Cut | Transform characters and extract fields |
| Day 3 | `03_day3_rev_sort_uniq_and_pager.md` | A Trick with Rev; Sort and Unique; Don't Forget Your Pager | Build useful pipelines and inspect results |
| Day 4 | `04_day4_final_lab_and_review.md` | Summary + applied review | Use Ch. 6 with TLCL Ch. 19/20 skills |

## Required discipline

Before every command, he must write or say:

```text
Question:
Input text:
First command:
What I expect to see:
Possible wrong result:
How I will verify:
```

After every command, he must answer:

```text
Did the output match my prediction?
What did this command remove, keep, or change?
Should I inspect before adding another pipeline stage?
```

## Setup

Create a safe practice folder:

```bash
mkdir -p ~/effective-shell-ch06-practice
cd ~/effective-shell-ch06-practice
```

Create sample data:

```bash
cat > movies.csv <<'EOF'
Rank,Rating,Title,Reviews
1,97,Black Panther (2018),515
2,94,Avengers: Endgame (2019),531
3,93,Us (2019),536
4,97,Toy Story 4 (2019),445
5,99,Lady Bird (2017),393
6,100,Citizen Kane (1941),94
7,97,Mission: Impossible - Fallout (2018),430
8,98,The Wizard of Oz (1939),120
9,96,The Irishman (2019),441
10,97,Get Out (2017),382
EOF

cat > app.log <<'EOF'
2026-07-01T08:00:01Z info - Request: GET /index.html
2026-07-01T08:00:02Z info - Serving file /var/www/index.html
2026-07-01T08:01:10Z error - Permission denied reading /var/www/private.html
2026-07-01T08:01:11Z warn - Retrying request
2026-07-01T08:02:44Z error - Permission denied reading /var/www/private.html
2026-07-01T08:03:01Z info - Request: GET /images/logo.png
2026-07-01T08:04:20Z error - Missing file /var/www/favicon.ico
2026-07-01T08:04:21Z info - Returning 404
EOF

cat > pods.txt <<'EOF'
NAME                                  READY   STATUS    RESTARTS   AGE
web-frontend-74fd8                    1/1     Running   0          3d
api-server-6bb9c                      1/1     Running   2          3d
db-primary-0                          1/1     Running   0          12d
cache-redis-0                         1/1     Running   1          12d
worker-batch-11                       0/1     CrashLoopBackOff  7  2h
EOF
```

## Final standard

He is ready to move on when he can explain:

```text
head/tail inspect position.
tr works on characters, not words.
cut extracts fields or character positions.
rev can make “last field” extraction possible with simple tools.
sort before uniq when duplicates are not adjacent.
less is part of disciplined inspection.
A pipeline should be built and verified one stage at a time.
```
