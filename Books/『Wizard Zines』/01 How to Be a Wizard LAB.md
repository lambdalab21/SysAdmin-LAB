Yes. Do one short exercise immediately after reading it. Do **not** assign another book.

Evans describes the zine as being about how she learns difficult programming topics: asking good questions, reading source code, debugging, software design, and developing the ability to “figure it out anyway.” ([wizard zines](https://wizardzines.com/zines/wizard/?utm_source=chatgpt.com "So you want to be a wizard")) The zine also argues that deeper understanding is most worth the effort when debugging a difficult problem, improving performance, or building something new.

For him, the correct follow-up is a **small investigation journal**.

# First exercise: explain one thing that recently felt magical

Use the recent `scp` permission error.

Ask him to answer this question:

> Why could I log in to `app01` successfully with SSH, but still fail to copy files into `/var/www/site1/public/`?

He should investigate rather than search for a ready-made answer.

## Step 1: Write an initial hypothesis

Before running commands:

```text
I think scp fails because ...
```

A wrong hypothesis is acceptable. No hypothesis is not.

## Step 2: Collect evidence

From the development machine:

```bash
ssh deploy@app01
```

On `app01`:

```bash
whoami
id
ls -ld /var /var/www /var/www/site1 /var/www/site1/public
namei -l /var/www/site1/public
touch /var/www/site1/public/test.txt
```

Then from the development machine:

```bash
scp -v hello.txt deploy@app01:/var/www/site1/public/
```

## Step 3: Explain the result in plain English

A good answer should be close to:

> SSH authentication and filesystem authorization are different checks. SSH allowed the user `deploy` to enter the machine. But copying a file required `deploy` to have write and traversal permissions on the destination path. `scp` failed because one of those filesystem checks failed.

## Step 4: Explain how to prove the fix worked

For example:

```bash
touch /var/www/site1/public/test.txt
rm /var/www/site1/public/test.txt
scp hello.txt deploy@app01:/var/www/site1/public/
```

The point is not the exact fix. The point is the sequence:

```text
question → hypothesis → evidence → explanation → verification
```

# Create a “wizard notebook”

Use one Markdown file:

```text
wizard-notes.md
```

Template:

```markdown
# Question

Why could SSH work while scp failed?

## Initial hypothesis

## Evidence collected

## Commands used

## What I learned

## What I misunderstood

## How I verified the fix

## One follow-up question
```

Add one entry each week.

# Good weekly questions

Use real questions from his current work:

1. Why does `systemctl start nginx` differ from `systemctl enable nginx`?
    
2. Why can `ping app01` work while `curl http://app01` fails?
    
3. Why can Nginx run but return `403 Forbidden`?
    
4. Why does `scp -r ./dist app01:/tmp/` create a different directory structure than `scp -r ./dist/. app01:/tmp/`?
    
5. Why does a process listening on port `80` prevent another process from using that port?
    
6. Why does `sleep 300` appear in a sleeping process state?
    
7. Why does `yes > /dev/null` consume CPU even though no output appears on screen?
    
8. Why can `dig app01` and `getent hosts app01` produce different results?
    

# Add a “teach it back” session

After he finishes the exercise, ask him to explain it to you in two minutes without notes.

Evans explicitly identifies teaching or blogging about a topic as useful because it exposes gaps in understanding and forces a return to basic questions.

Use these prompts:

```text
What failed?
What evidence narrowed it down?
What did you initially misunderstand?
Which layer was responsible?
How would you diagnose it faster next time?
```

# One controlled “read the source” exercise

The zine recommends reading source code, but do not send him into the Linux kernel yet. Use readable configuration and scripts first.

For example:

```bash
systemctl cat nginx
```

Then:

```bash
cat /usr/lib/systemd/system/nginx.service
```

Ask:

1. Which command starts Nginx?
    
2. Which command validates the config before reload?
    
3. Under which target does the service start?
    
4. What does `ExecStartPre` do?
    
5. What does `ExecReload` do?
    

This gives him the habit of looking beneath an abstraction without drowning in complexity.

# One-hour follow-up plan

|Time|Activity|
|--:|---|
|10 min|Choose one real question|
|10 min|Write a hypothesis|
|20 min|Run commands and gather evidence|
|10 min|Write a plain-English explanation|
|10 min|Explain it aloud without notes|

That is enough.

The main lesson from _So You Want to Be a Wizard_ should not be:

> “I finished a zine.”

It should be:

> “When something feels magical, I know how to turn it into a question and investigate it.”