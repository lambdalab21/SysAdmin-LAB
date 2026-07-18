 #Book/The-Linux-Command-Line 
#shell-script #bash-script 
# TLCL Chapter 25: Starting a Project

Use this after Chapter 24, “Writing Your First Script.”

Chapter 25 is not mainly about memorizing new syntax. It is about starting to think like a script writer:

```text
What output should this script produce?
What data belongs in the script?
What should be a variable?
What should stay constant?
How do I make the script readable enough to improve later?
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch25-project/bin ~/tlcl-ch25-project/work ~/tlcl-ch25-project/versions
cd ~/tlcl-ch25-project
```

---
# Session 2: Second Stage — Adding a Little Data

## Section to read before exercises

Read only:

```text
Second Stage: Adding a Little Data
```

## After reading: concept questions

1. What kind of data is the script beginning to add? The script is beginning to add static HTML structures and headings. 
2. Why should output structure matter? Output structure matters because it creates valid and readable HTML. 
3. Why is it better to add a little data before adding logic? Adding a little data first keeps changes small and easy to talk. 
4. What would make the script harder to debug? Adding too much logic or data at once would make debugging more difficult. 
5. How will he know whether the script still works? I know that it works by running and checking the output after each change. This isn't a very good question. 

## Exercise 1: Copy the previous version

```bash
cd ~/tlcl-ch25-project
cp bin/sys_report_page versions/sys_report_page_session1
```

Question:

```text
Why save a previous working version? If the new version breaks, the previous working version can be restored.
```

## Exercise 2: Add a title and body

Edit:

```bash
nano bin/sys_report_page
```

Change it to:

```bash
#!/usr/bin/env bash

# Program to output a system information page

echo "<html>"
echo "<head>"
echo "  <title>System Information Report</title>"
echo "</head>"
echo "<body>"
echo "  <h1>System Information Report</h1>"
echo "</body>"
echo "</html>"
```

## Run and inspect

```bash
bash bin/sys_report_page
./bin/sys_report_page > work/report.html
cat work/report.html
```

Check line count:

```bash
wc -l work/report.html
```

## Exercise 3: Break it deliberately, then fix it

Make a temporary mistake: remove one closing quote from an `echo` line.

Run:

```bash
bash bin/sys_report_page
```

Then restore the quote and run again.

## Explain what happened

Answer:

```text
What error did Bash show? 
Was the problem in HTML or shell syntax?
How did I know where to look?
```
Bash showed a syntax error. The problem was in  the shell syntax, not HTML. The error message pointed to the line with the missing quote, making locations obvious.