# TLCL Chapter 25: Starting a Project

Use this after Chapter 24, “Writing Your First Script.”

One-time setup:

```bash
mkdir -p ~/tlcl-ch25-project/bin ~/tlcl-ch25-project/work ~/tlcl-ch25-project/versions
cd ~/tlcl-ch25-project
```

Start each session with:

```bash
pwd
ls -la
```

Then answer:

```text
Where am I?
What files already exist?
What will I create or modify today?
How will I test it?
```

---
# Session 4: Assigning Values to Variables and Constants

## Section to read before exercises

Read only:

```text
Assigning Values to Variables and Constants
```

## After reading: concept questions

1. Why are spaces around `=` wrong in shell variable assignment? Spaces around = are wrong because Bash treats it as separate words, not assignment. 
2. What is the difference between `NAME=value` and `$NAME`? NAME=value assigns a value, $NAME gets the value of the variable. 
3. When do you use the variable name without `$`? Use the variable name without $ when assigning a value. 
4. When do you use the variable name with `$`? Use the variable name with $ when you want to expand or read its value. 
5. What is command substitution useful for? Command substitution is useful for storing the output of a command in a variable. 

## Exercise 1: Test assignment syntax

Run:

```bash
TITLE="Correct Assignment"
echo "$TITLE"
```

Now deliberately run:

```bash
TITLE = "Wrong Assignment"
```

Then:

```bash
echo "$TITLE"
```

Explain:

```text
What did Bash try to do with TITLE = "Wrong Assignment"? Bash tries to run a command named TITLE WITH = and "wrong assignment" as arguments. 
Why is that not assignment? That is not assignment because assignment can not have space around =. 
```

## Exercise 2: Add dynamic values

Edit `bin/sys_report_page`:

```bash
nano bin/sys_report_page
```

Use:

```bash
#!/usr/bin/env bash

# Program to output a system information page

TITLE="System Information Report"
CURRENT_TIME=$(date)
HOST_NAME=$(hostname)

echo "<html>"
echo "<head>"
echo "  <title>$TITLE</title>"
echo "</head>"
echo "<body>"
echo "  <h1>$TITLE</h1>"
echo "  <p>Generated: $CURRENT_TIME</p>"
echo "  <p>Host: $HOST_NAME</p>"
echo "</body>"
echo "</html>"
```

## Predict before running

Answer:

```text
When is date executed? date and hostname are executed when the script runs. 
What will CURRENT_TIME contain? CURRENT_TIME will contain the output of a date.  
What will HOST_NAME contain? HOST_NAME will contain the output of hostname. 
Will the values change if I run the script again later? Yes, the values can change if you run the script again later, especially CURRENT_TIME. 
```

## Run and inspect

```bash
bash bin/sys_report_page > work/report.html
cat work/report.html
```

Run again after a few seconds:

```bash
sleep 3
bash bin/sys_report_page > work/report2.html
diff work/report.html work/report2.html || true
```

## Explain the difference

Answer:

```text
Which line changed? The generated line changed because the time was different. 
Why did it change? It changed because date runs each time the script is executed. 
Which value stayed the same? The host line stayed the same because the machine name did not change. 
Why did it stay the same? It stayed the same because hostname returned the same value both times. 
```

## Exercise 3: Read documentation habit

Use documentation before adding any new command substitution:

```bash
date --help | head
man date
hostname --help 2>/dev/null | head || man hostname
```

Answer:

```text
Which option would let date print only year-month-day? date +%F prints just the year-month-day format. 
Where did I find that information? I found that from the man page. 
```

Try:

```bash
date +%F
```

Then update:

```bash
CURRENT_DATE=$(date +%F)
```