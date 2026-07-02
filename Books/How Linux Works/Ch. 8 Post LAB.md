
The wrong way is:

```text
read instructions → copy commands → see output → move on
```

That produces familiarity, not understanding.

The right way is:

```text
predict → run → observe → explain → break → diagnose → repeat later
```

## The basic rule

Before each lab, make him answer:

1. What do I think will happen?
    
2. Which process, file, port, or resource should change?
    
3. Which command will prove it?
    
4. What result would surprise me?
    

Then run the experiment.

Afterward, ask:

1. What actually happened?
    
2. Was my prediction correct?
    
3. Which tool gave the strongest evidence?
    
4. What did I misunderstand?
    
5. How would I detect this problem on a real server?
    

That is how the lab becomes durable knowledge.

# If he cannot answer a question

Do not immediately give him the answer.

Use this sequence.

## Step 1: Ask him to inspect the evidence again

Example question:

> Why did `sleep` show state `S`?

Have him run:

```bash
ps -p <PID> -o pid,stat,comm,args
```

Then ask:

> What does `S` mean? What is `sleep` waiting for?

He should use:

```bash
man ps
```

Search inside the man page:

```text
/PROCESS STATE
```

The lesson is not only the answer. The lesson is how to recover the answer.

## Step 2: Reduce the question

If he still cannot answer:

Bad question:

> Explain process state.

Better sequence:

1. Is the process running right now?
    
2. Is it using CPU?
    
3. Is it waiting for something?
    
4. What letter appears in `STAT`?
    
5. What does the man page say that letter means?
    

Break the problem into smaller pieces.

## Step 3: Ask for a hypothesis

He should say something like:

> “I think `sleep` is not using CPU because it is waiting for a timer.”

Then verify.

Even a wrong hypothesis is useful if it is testable.

## Step 4: Use one tool, not five

Beginners often panic and run random commands.

Do not allow this:

```bash
top
ps
free
vmstat
journalctl
strace
lsof
```

all at once with no reason.

Ask:

> What is the simplest tool that can answer the current question?

Examples:

|Question|First tool|
|---|---|
|Is the process running?|`ps`|
|Which process uses CPU?|`top`|
|Which process owns port 8000?|`ss` or `lsof`|
|Is memory pressure increasing?|`free`, then `vmstat`|
|Why can’t a file open?|`strace`|
|Why did a service fail?|`systemctl status`, then `journalctl`|

## Step 5: Read the man page only for the needed part

He should not read an entire man page every time.

Example:

```bash
man vmstat
```

Search:

```text
/runnable
/wa
/si
/so
```

That is enough.

The habit should be:

```text
question → targeted lookup → test → explain
```

not:

```text
read 12 pages of documentation → forget everything
```

# Use a lab journal

Have him keep one Markdown file per chapter.

Example:

```text
ch8-lab-notes.md
```

Use this template.

````markdown
# Lab: CPU-heavy process

## Prediction
I expect `yes > /dev/null` to use close to one CPU core.

## Commands
```bash
yes > /dev/null &
top
````

## Observation

The `yes` process moved near the top and used high CPU.

## Explanation

`yes` continuously writes output, so it remains runnable and consumes CPU.

## Tool that proved it

`top`

## Real-world connection

A runaway loop in an application could produce similar symptoms.

## What I did not understand

Why load average did not immediately match CPU percentage.

````

The last section matters. Unanswered questions should become the next review target.

# Three levels of understanding

He should not move on merely because commands worked.

## Level 1: Recognition

He sees:

```bash
vmstat 1 5
````

and remembers:

> “That shows system activity.”

Too weak.

## Level 2: Operational use

He knows:

> “I can use `vmstat` to inspect CPU, memory, paging, and I/O activity.”

Acceptable.

## Level 3: Diagnostic reasoning

He sees:

```text
r high
id low
wa low
```

and says:

> “The system likely has CPU contention, not storage wait.”

That is the real target.

He does not need Level 3 immediately for every tool, but that is where the labs should lead.

# Best way to repeat labs

Do not repeat the full written lab every time.

Use three passes.

## Pass 1: Guided

He follows instructions carefully.

Goal:

- understand commands
    
- observe output
    
- ask questions
    

## Pass 2: Partially guided

Give only the problem.

Example:

> Find the process using port 8000.

He decides:

```bash
ss -tulpn
lsof -i :8000
```

## Pass 3: Blind troubleshooting

Create a mystery failure.

Example:

- start `python3 -m http.server 8000`
    
- ask him why another service cannot bind port 8000
    

He must choose the tools without hints.

That progression matters more than adding more labs.

# If he gets stuck for too long

Use this limit:

```text
10 minutes of honest investigation
```

After that, give a hint, not the full answer.

Good hint:

> “Check whether the destination directory is writable by `deploy`.”

Bad answer:

```bash
sudo chown -R deploy:nginx /var/www/site1
```

Why? Because the answer removes the thinking.

The hint should point to the next layer, not solve the problem.

# Ask “why” after every command

Example:

```bash
ss -tulpn | grep ':80'
```

Do not accept:

> “It checks port 80.”

Ask:

1. Why use `ss`?
    
2. What does `-t` mean?
    
3. What does `-l` mean?
    
4. What does `-p` show?
    
5. Why pipe to `grep`?
    
6. What would empty output mean?
    
7. What would a line of output prove?
    
8. What would it not prove?
    

That last question is important.

For example:

```text
ss shows Nginx listening on port 80
```

does **not** prove:

```text
a remote client can reach it through the firewall
```

# Good failure policy

He should make mistakes intentionally.

For each lab, include one controlled failure:

|Topic|Controlled failure|
|---|---|
|Permissions|remove directory write permission|
|Process|stop a process with `SIGSTOP`|
|Port conflict|bind port 8000 with another process|
|Files|rename required config file|
|Memory|run controlled allocation script|
|CPU|start several `yes` processes|
|Nginx|change web root to wrong directory|
|Firewall|remove HTTP service rule temporarily|

Then require:

```text
symptom
→ evidence
→ hypothesis
→ fix
→ verification
```

This is better than smooth success.

# Oral explanation matters

After each lab, ask him to explain without looking at notes.

Example:

> “Why could SSH login work while `scp` to `/var/www/site1/public` fails?”

Good answer:

> “SSH authentication succeeded, but the remote `deploy` user lacked filesystem write permission on the destination directory. Authentication and directory permissions are separate checks.”

That kind of explanation proves understanding.

# Use short review cards

At the end of each chapter, create 10–15 high-value prompts.

Examples for Ch. 8:

```text
What is the difference between a program and a process?
Why is sleeping state often normal?
What does PPID mean?
Difference between SIGTERM and SIGKILL?
When would you use lsof?
When would you use strace?
What does vmstat reveal?
Why is available memory more useful than free memory?
What is niceness?
What is a cgroup?
```

Review them:

```text
next day
3 days later
1 week later
2 weeks later
1 month later
```

That spacing matters more than rereading the chapter.

# A practical scoring system

After each lab, score him 0–3.

|Score|Meaning|
|--:|---|
|0|Could not explain or reproduce|
|1|Followed instructions but needed help|
|2|Reproduced and explained with minor hints|
|3|Diagnosed a modified version independently|

Do not move on just because he completed the steps.

Move on when most core labs are at level 2, and at least a few are level 3.

# For Ch. 8 specifically

He is ready for Ward Ch. 9 when he can independently do these:

```bash
ps -p <PID> -o pid,ppid,stat,comm,args
top
free -h
vmstat 1 5
pidstat -p <PID> 1 5
ss -tulpn
lsof -i :<PORT>
strace -Z -e trace=%file <COMMAND>
```

And explain when to use each one.

He does **not** need to memorize every flag.

He does need to answer:

> “What evidence am I looking for, and why is this the right tool?”

# Best parent/mentor behavior

Do not rescue too quickly.

Ask questions in this order:

1. What symptom do you see?
    
2. What layer might be failing?
    
3. What evidence do you already have?
    
4. Which command would narrow the possibilities?
    
5. What result do you predict?
    
6. What changed after the fix?
    
7. How would you undo the fix?
    

That builds an infrastructure engineer.

The objective is not:

```text
finish the lab sheet
```

It is:

```text
develop the habit of observing systems, forming hypotheses, and verifying them with evidence
```