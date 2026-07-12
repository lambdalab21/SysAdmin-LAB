#Book/The-Linux-Command-Line Author/Shotts 
#text-processing #regex
# Chapter 20 Text Processing Practice Using Chapter 19 Regex

A cleaner phrasing: “Can you make Chapter 20 text-processing practice exercises that use regular expressions from Chapter 19?”

Recommended order:

| Day | File | Focus |
|---|---|---|
| Day 1 | `01_regex_row_selection_and_counting.md` | `grep`, anchors, alternation, `wc`, `sort`, `uniq -c` |
| Day 2 | `02_regex_with_cut_fields.md` | regex row selection plus `cut` field extraction |
| Day 3 | `03_regex_with_sed_transformations.md` | `sed` substitutions, delete, print selected lines |
| Day 4 | `04_config_file_regex_practice.md` | SSH/Nginx-style config inspection |
| Day 5 | `05_package_passwd_realistic_practice.md` | `/etc/passwd` and `apt list` style output |
| Day 6 | `06_logs_processes_network_report.md` | logs, `ps`, `ss`, reports |
| Day 7 | `07_mixed_final_lab.md` | final mixed review lab |

Core habit:

```text
English rule first → regex second → inspect output → add text processing → explain final answer.
```

Bad learning:

```text
copy command → see output → move on
```

Good learning:

```text
predict → run one stage → inspect → explain → add next stage → verify
```

Use these files after he has already read Chapter 19 and Chapter 20 once.
