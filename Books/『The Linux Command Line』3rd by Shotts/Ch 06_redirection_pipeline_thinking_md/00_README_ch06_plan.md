#Book/The-Linux-Command-Line Author/Shotts 
#README 
#redirection
# TLCL Chapter 6 — Redirection and Pipeline Thinking Plan

Use this package while reading Author/Shotts Chapter 6.

Recommended split:

| Session | File | Focus |
|---|---|---|
| 1 | `01_streams_stdout_stderr_stdin.md` | stdin, stdout, stderr, file descriptors |
| 2 | `02_redirection_overwrite_append_errors.md` | `>`, `>>`, `2>`, `2>>`, `/dev/null` |
| 3 | `03_pipelines_filters_stage_by_stage.md` | `|`, filters, `sort`, `uniq`, `wc`, `grep`, `head`, `tail` |
| 4 | `04_tee_and_pipeline_debugging.md` | `tee`, intermediate inspection, debugging pipelines |
| 5 | `05_applied_lab_and_self_test.md` | realistic tasks and retention test |

Main habit:

```text
Predict → run → inspect → explain → add one more stage.
```

The student should not type long pipelines from the guide without explaining them first.

Core Feynman analogy:

```text
A command is like a small machine.
stdin is what goes into the machine.
stdout is the useful product.
stderr is the warning/error chute.
redirection sends a stream to a file.
a pipe sends stdout from one machine into stdin of the next machine.
```
