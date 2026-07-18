 #Book/The-Linux-Command-Line 
#shell-script #bash-script 
# TLCL Chapter 25: Starting a Project

Use this after Chapter 24, “Writing Your First Script.”

One-time setup:

```bash
mkdir -p ~/tlcl-ch25-project/bin ~/tlcl-ch25-project/work ~/tlcl-ch25-project/versions
cd ~/tlcl-ch25-project
```

---
# Session 1: First Stage — Minimal Document

## Section to read before exercises

Read only:

```text
First Stage: Minimal Document
```

## After reading: concept questions

Answer without looking back:

1. Why should the first version be minimal? The first version should be minimal so that you have something working quickly. 
2. What is the danger of adding too much too soon? Adding too much too soon makes debugging difficult and increases the risk of failure. 
3. What does “working” mean at this stage? Working means that the script runs without errors and produces a basic output. 
4. How does this connect to Chapter 24?
5. Why should he test the script before improving it?

## Exercise 1: Write the purpose first

Before typing a script, write this in `work/project-notes.md`:

```bash
cat > work/project-notes.md <<'EOF'
# Chapter 25 Project Notes

Purpose of first script:

Expected first output:

How I will test it:
EOF
```

## Exercise 2: Create the minimal script

Create:

```bash
cd ~/tlcl-ch25-project
nano bin/sys_report_page
```

Type:

```bash
#!/usr/bin/env bash

# Program to output a system information page

echo "<html>"
echo "</html>"
```

## Run safely first with bash

```bash
bash bin/sys_report_page
```

Then make it executable:

```bash
chmod 755 bin/sys_report_page
./bin/sys_report_page
```

## Verify

```bash
./bin/sys_report_page > work/report.html
cat work/report.html
```

Open with text tools first:

```bash
head work/report.html
wc -l work/report.html
```