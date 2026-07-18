#Book/Effective-Shell #Author/Kerr
# Effective Shell Chapter 4: Regular Expression Essentials — Study Plan

A cleaner phrasing: “Create Markdown file(s) for Chapter 4, ‘Regular Expression Essentials,’ by Kerr. Use the Feynman method and train disciplined thinking rather than mindless command typing.”

Use this after Author/Shotts Chapter 19.

Recommended schedule:

| Session | File | Focus |
|---|---|---|
| 1 | `01_intro_and_building_from_examples.md` | why regex gets complex; build from examples |
| 2 | `02_quantifiers_and_false_matches.md` | `*`, `+`, `?`, `{}` and false positives |
| 3 | `03_character_sets_metacharacters.md` | ranges, character sets, metacharacters, escaping |
| 4 | `04_anchors_capture_greedy.md` | `^`, `$`, groups, greedy matching |
| 5 | `05_tool_differences_and_manpage_habits.md` | regex dialects, `man`, `grep -E`, portability |
| 6 | `06_final_lab_regex_as_disciplined_tool.md` | final mixed lab and self-test |

Chapter 4’s important learning goal is not “memorize more symbols.” It is:

```text
Build regexes gradually and explain what each piece does.
```

Effective Shell Chapter 4 says regular expressions can be intimidating, but the way to manage complexity is to start simple and add complexity only as needed. The official chapter also emphasizes examples, quantifiers, character sets/metacharacters, anchors, capture groups, greedy/lazy matching, and tool differences.

Core habit:

```text
Examples first
→ English rule
→ simplest regex
→ test false positives
→ test false negatives
→ explain every symbol
```

Do not let the student copy regex examples as magic spells.
