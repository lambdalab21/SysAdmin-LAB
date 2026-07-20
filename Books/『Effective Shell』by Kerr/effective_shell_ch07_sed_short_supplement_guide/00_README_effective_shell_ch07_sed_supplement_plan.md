# Effective Shell Ch. 7 — Advanced Text Manipulation with `sed`

Supplement to *The Linux Command Line* by William Shotts, especially TLCL Ch. 19 **Regular Expressions** and Ch. 20 **Text Processing**.

This guide is intentionally short. Most of the basic ideas were already introduced in TLCL. The purpose here is to refresh them and add a few effective habits from Kerr:

```text
inspect the text
→ state the transformation in English
→ build the smallest sed command
→ preview to stdout
→ test false positives
→ write to a new file
→ replace original only after inspection
```

## Suggested split

| Day | File | Main gain |
|---|---|---|
| Day 1 | `01_day1_sed_refresh_and_substitution.md` | Refresh what `sed` is, how substitution works, and how line addresses control where edits happen |
| Day 2 | `02_day2_multiple_expressions_comments_and_line_edges.md` | Use multiple expressions, strip comments/blank lines, append/prepend text safely |
| Day 3 | `03_day3_capture_groups_templates_and_safe_editing.md` | Use capture groups, backreferences, simple templates, and safe alternatives to risky in-place editing |
| Review | `04_review_quiz_and_retention_drills.md` | Check retention and practice choosing between `grep`, `sed`, `awk`, and a real program |

## Reading rule

Do not read passively.

Before each reading section, ask:

```text
What am I trying to become able to do after this section?
What problem does this section help me solve faster?
What mistake is this section warning me against?
```

After each reading section, answer the concept questions before typing commands.

## Minimum standard after this supplement

He should be able to explain and safely use:

```bash
sed 's/old/new/' file
sed 's/old/new/g' file
sed -E 's/(pattern)/replacement/' file
sed -n '/pattern/p' file
sed '/pattern/d' file
sed -e 'expr1' -e 'expr2' file
sed 's/$/suffix/' file
sed 's/^/prefix/' file
```

He should also understand:

```text
sed reads input line by line.
sed normally writes changed text to stdout.
A sed pattern can be a regex.
Addresses choose which lines receive a command.
Capture groups let us reuse matched text.
In-place editing should be treated cautiously.
If the sed command becomes unreadable, use awk or a small program instead.
```
