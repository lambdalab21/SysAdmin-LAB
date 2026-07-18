#Book/Effective-Shell #Author/Kerr 
#drill #find #search 
# Effective Shell Chapter 3: Finding Files — Study Plan

A cleaner phrasing: “Create a reading guide for Chapter 3, ‘Finding Files and Folders,’ of *Effective Shell* that solidifies what he learned in Chapter 17 of *The Linux Command Line*.”

Use these files after he has already studied Author/Shotts Chapter 17.

Recommended schedule:

| Session | File | Focus |
|---|---|---|
| 1 | `01_reading_guide_find_structure.md` | Read Effective Shell Ch. 3 actively |
| 2 | `02_reinforcement_lab_find_workflow.md` | Apply the chapter to lab tasks |
| 3 | `03_self_test_retention.md` | Check retention and disciplined thinking |

The most important lesson from Effective Shell Ch. 3:

```text
find is not a normal command with ordinary options after the command name.
It is closer to a small search language.
```

Use this mental model:

```text
find WHERE TESTS ACTION
```

Examples:

```bash
find . -type f -name "*.logs" -print
find . \( -name "*.js" -or -name "*.html" \) -not -path "*programs*"
find . -type f -mtime -2 -ls
```

Do not let the student just type these. For each command, he must explain:

```text
WHERE:
TESTS:
ACTION:
RISK:
VERIFICATION:
```
