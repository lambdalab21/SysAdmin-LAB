# TLCL Chapter 28: Reading Keyboard Input — Two-Day Guide

## Continuing project

Use the same project directory pattern as earlier chapters:

```bash
mkdir -p ~/tlcl-ch28-input
cd ~/tlcl-ch28-input
```

The continuing script will be:

```text
interactive-report.sh
```

It builds on the earlier report-generator idea from Chapters 25–27.
## Completion standard

He is done with Chapter 28 only if he can explain:

```text
read reads one line of standard input.
read assigns words to variables.
REPLY is used when no variable name is supplied.
-r usually makes input safer by preventing backslash interpretation.
-p gives a prompt.
IFS affects how input is split.
A here string can feed one string to read.
A pipeline can make read run in a subshell.
User input must be validated before being trusted.
A menu is just controlled input plus branching.
```
