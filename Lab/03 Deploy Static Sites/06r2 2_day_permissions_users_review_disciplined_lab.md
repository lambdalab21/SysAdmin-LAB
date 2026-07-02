# Two-Day Review: Permissions, Users, Groups, and Disciplined Thinking

**Reading focus**

- Shotts, *The Linux Command Line*, Chapter 9: Permissions
- Ward, *How Linux Works*, Sections 7.3 and 7.4: user management files and commands

**Purpose**

This review is not mainly about memorizing commands.

It is about learning to think like this:

```text
question → command → evidence → conclusion → verification
```

The recent SSH/PAM problem showed an important weakness: rushing past evidence. This review is designed to slow you down at the exact places where mistakes happen.

---

# The Habit You Are Practicing

Before an important command, write:

```text
I am about to run:

This command tests:

If my idea is correct, I expect:

If my idea is wrong, I expect:
```

After the command, write:

```text
Exact output:

What this proves:

What this does NOT prove:

Next narrow question:
```

Do not write only:

```text
worked
failed
probably
I don't know
```

Those are not evidence.

---

# Feynman Explanation: Permissions

Imagine a school building.

A file is like a worksheet.
A directory is like a hallway or classroom.

For a file:

```text
read    = look at the worksheet
write   = change the worksheet
execute = run it as a program
```

For a directory:

```text
read    = list names inside the room
write   = add or remove names in the room
execute = enter/traverse the room
```

The most important surprise:

```text
execute on a directory does not mean run it.
execute on a directory means you can pass through it.
```

That matters for Nginx.

Nginx may have permission to read `index.html`, but if it cannot pass through `/var`, `/var/www`, `/var/www/site1`, or `/var/www/site1/public`, it still cannot serve the file.

That is why `namei -l` is so useful.

---

# Feynman Explanation: Users and Groups

Linux asks:

```text
Who are you?
What groups are you in?
What does this file allow your identity to do?
```

A user is an identity.
A group is a team.
Permissions decide what the owner, group, and everyone else can do.

For the website:

```text
deploy = the user who copies files
nginx  = the user/group that reads files for the web server
```

The goal is not:

```text
make everything writable
```

The goal is:

```text
deploy can write website files
nginx can read website files
nginx should not need to write website files
```

---

# Day 1: Shotts Chapter 9 — Permissions

## Goal

By the end of Day 1, you should be able to explain why a file is readable, writable, executable, or blocked.

You should also be able to diagnose basic website permission problems without guessing.

---

## Part 1: Read with a Purpose

Read or review Shotts Chapter 9.

Focus on:

- owners
- groups
- others
- `r`, `w`, `x`
- file permissions vs directory permissions
- `chmod`
- `chown`
- `chgrp`
- `umask`
- `su`
- `sudo`

Do not try to memorize every mode number yet.

First understand what the permissions mean.

---

## Stop Point 1: Explain Before Commands

Before touching the keyboard, explain this in your own words:

```text
A file permission answers:
Who can read, change, or execute this file?

A directory permission answers:
Who can list, create/delete, or pass through this directory?
```

Green check:

```text
[ ] I can explain the difference between file `x` and directory `x`.
```

Do not continue until this is clear.

---

## Part 2: Small Local Permission Lab

On your development machine or `app01`:

```bash
mkdir -p ~/perm-review-lab
cd ~/perm-review-lab
pwd
whoami
```

Create a file and a directory:

```bash
touch note.txt
mkdir hallway
ls -l
ls -ld hallway
```

Before continuing, answer:

```text
What is the owner of note.txt?
What is the group of note.txt?
What can the owner do?
What can the group do?
What can others do?
```

Green check:

```text
[ ] I identified owner, group, and other from `ls -l`.
```

---

## Part 3: Change File Permissions Carefully

Run:

```bash
chmod 600 note.txt
ls -l note.txt
```

Write:

```text
Command:
chmod 600 note.txt

What changed:

What 600 means:

Who can read the file now:

Who can write the file now:
```

Now run:

```bash
chmod 644 note.txt
ls -l note.txt
```

Write:

```text
What 644 means:

Why 644 is common for web files:
```

Green check:

```text
[ ] I can explain 600 and 644 without guessing.
```

---

## Part 4: Directory Permissions Matter More Than You Think

Run:

```bash
chmod 755 hallway
ls -ld hallway
```

Create a file inside it:

```bash
touch hallway/inside.txt
ls -l hallway/inside.txt
```

Now remove execute permission from the directory:

```bash
chmod 644 hallway
ls -ld hallway
```

Try:

```bash
ls hallway
cat hallway/inside.txt
```

Write:

```text
What failed?

Why did it fail?

What does directory execute permission mean?
```

Restore:

```bash
chmod 755 hallway
```

Green check:

```text
[ ] I understand why parent directory permissions can block access to a file.
```

Slow down here. This is one of the most important lessons for Nginx 403 errors.

---

## Part 5: Apply This to `site1`

On `app01`:

```bash
ls -ld /var
ls -ld /var/www
ls -ld /var/www/site1
ls -ld /var/www/site1/public
ls -l /var/www/site1/public/index.html
```

Now use the better tool:

```bash
namei -l /var/www/site1/public/index.html
```

Take time here.

`namei -l` shows every directory in the path. Do not look only at `index.html`.

Write:

```text
Can the path be traversed all the way to index.html?

Which directory permissions matter?

Which file permission matters?

What user writes the file?

What user or group reads the file?
```

Now test as Nginx:

```bash
sudo -u nginx test -r /var/www/site1/public/index.html && echo "nginx can read"
```

If it prints:

```text
nginx can read
```

write what that proves.

If it prints nothing, run:

```bash
echo $?
```

and inspect the path again with:

```bash
namei -l /var/www/site1/public/index.html
```

Green check:

```text
[ ] I proved whether Nginx can read index.html.
```

---

## Part 6: Test Deploy User Write Access

Run:

```bash
sudo -u deploy test -w /var/www/site1/public && echo "deploy can write"
```

Then:

```bash
sudo -u nginx test -w /var/www/site1/public && echo "nginx can write"
```

Expected idea:

```text
deploy should be able to write.
nginx usually should not need to write.
```

Write:

```text
Can deploy write?

Can nginx write?

Is that the desired setup?

Why?
```

Green check:

```text
[ ] I checked write access using the actual users, not guessing from memory.
```

---

## Day 1 Reflection

Write no more than 8 lines:

```text
Today I learned:

The most important command was:

The most important mistake to avoid is:

One thing I can now prove with a command is:
```

Do not write a long essay.

---

# Day 2: Ward 7.3 and 7.4 — Users, Groups, and Account Files

## Goal

By the end of Day 2, you should be able to answer:

```text
Who is this user?
What groups is the user in?
What shell does the user get?
Is the account locked or expired?
Which files record this information?
Which commands safely inspect or modify it?
```

---

## Part 1: Read with a Purpose

Review Ward:

- 7.3 User Management Files
- 7.4 User Management Commands

Focus on:

- `/etc/passwd`
- `/etc/shadow`
- `/etc/group`
- UID
- GID
- home directory
- login shell
- `useradd`
- `usermod`
- `passwd`
- `chage`
- `groupadd`
- `gpasswd`

Do not memorize every command option. Tie every command to a question.

---

## Feynman Explanation: Account Files

Linux has record books.

```text
/etc/passwd = basic user identity record
/etc/shadow = password and account aging record
/etc/group  = team membership record
```

A line in `/etc/passwd` is like an ID card:

```text
name : password-placeholder : UID : GID : comment : home : shell
```

Example:

```text
deploy:x:1001:1001::/home/deploy:/bin/bash
```

Read it as:

```text
The user is deploy.
The UID is 1001.
The primary GID is 1001.
The home directory is /home/deploy.
The login shell is /bin/bash.
```

Slow down here.

In the SSH incident, the login shell mattered. Do not use only `echo $SHELL`; inspect the actual account record.

---

## Part 2: Inspect Important Users

On `app01`:

```bash
id deploy
id nginx
getent passwd deploy
getent passwd nginx
getent group nginx
getent group wheel
```

Write:

```text
User deploy UID:
User deploy groups:
User deploy home:
User deploy shell:

User nginx UID:
User nginx groups:
User nginx home:
User nginx shell:
```

Green check:

```text
[ ] I can identify UID, GID, groups, home directory, and shell for deploy and nginx.
```

---

## Part 3: Account State

Run:

```bash
sudo passwd -S deploy
sudo chage -l deploy
```

Write:

```text
Is the account locked?

Is the account expired?

What command proves that?
```

Slow down here.

A wrong password does not prove an account is locked.

A locked account must be proven with account-state commands or logs.

Green check:

```text
[ ] I did not confuse wrong password with locked account.
```

---

## Part 4: Use Questions, Not Random Commands

Complete this table.

| Question | Command | Evidence line |
|---|---|---|
| Does deploy exist? | `getent passwd deploy` | |
| What shell does deploy use? | `getent passwd deploy` | |
| Which groups is deploy in? | `id deploy` | |
| Does nginx exist? | `getent passwd nginx` | |
| Is deploy locked? | `sudo passwd -S deploy` | |
| Is deploy expired? | `sudo chage -l deploy` | |
| Can deploy write to the website? | `sudo -u deploy test -w /var/www/site1/public` | |
| Can nginx read index.html? | `sudo -u nginx test -r /var/www/site1/public/index.html` | |

Green check:

```text
[ ] Every command answered a specific question.
```

---

## Part 5: Connect Users, Groups, and Website Deployment

Inspect current website ownership:

```bash
ls -ld /var/www/site1
ls -ld /var/www/site1/public
ls -l /var/www/site1/public
```

Write:

```text
Owner of /var/www/site1:
Group of /var/www/site1:

Owner of public directory:
Group of public directory:

Why deploy needs write access:

Why nginx needs read access:

Why nginx does not normally need write access:
```

Now inspect the full path:

```bash
namei -l /var/www/site1/public/index.html
```

Write:

```text
The first directory that could block Nginx is:

The evidence is:
```

If nothing blocks Nginx, write:

```text
No directory in the path blocks Nginx because:
```

Green check:

```text
[ ] I connected users/groups to the actual deployment directory.
```

---

## Part 6: Controlled Break/Fix Permission Exercise

This is a safe experiment.

Before changing anything, record current permissions:

```bash
ls -l /var/www/site1/public/index.html
```

Break read access:

```bash
chmod 000 /var/www/site1/public/index.html
```

Test as Nginx:

```bash
sudo -u nginx test -r /var/www/site1/public/index.html && echo "nginx can read"
```

Request the page:

```bash
curl -v http://app01/
```

Inspect Nginx error log:

```bash
sudo tail -n 20 /var/log/nginx/site1.error.log
```

Now restore:

```bash
chmod 644 /var/www/site1/public/index.html
```

Test again:

```bash
sudo -u nginx test -r /var/www/site1/public/index.html && echo "nginx can read"
curl -v http://app01/
```

Write:

```text
Symptom:

Exact permission change:

Command that proved nginx could not read:

Log line or curl output:

Smallest fix:

Verification:
```

Green check:

```text
[ ] I broke one thing, observed it, fixed it, and verified it.
```

---

## Part 7: Five Whys, But Evidence-Based

Use the permission break/fix exercise.

Do not write vague answers.

Example:

```text
Problem: Browser cannot load site1.

Why 1: Nginx returned an error.
Evidence: curl showed HTTP status ____.

Why 2: Nginx could not read index.html.
Evidence: sudo -u nginx test -r returned ____.

Why 3: index.html had permission 000.
Evidence: ls -l showed ____.

Why 4: I changed it during the lab.
Evidence: command history / lab notes show chmod 000.

Why 5: I was testing whether permissions affect Nginx.
Evidence: lab instruction and before/after verification.
```

Now do your own.

Green check:

```text
[ ] Each why answer included evidence.
```

---

# Final Completion Test

Answer without notes first. Then verify with commands.

```text
1. Which user deploys website files?

2. Which user or group reads website files for Nginx?

3. Why should deployment not require root?

4. Why is 777 usually a bad fix?

5. What does directory execute permission mean?

6. What command shows every directory permission in a path?

7. What file stores the login shell?

8. What command shows a user's groups?

9. What command shows whether an account is locked?

10. What command proves Nginx can read index.html?
```

Green check:

```text
[ ] I answered first from understanding.
[ ] I verified with commands.
[ ] I corrected any answer that the command disproved.
```

---

# Final Short Reflection

Write this at the end:

```text
The mistake I am trying to stop making:

The habit I am trying to build:

One command I understand better now:

One place I slowed down and noticed evidence:

One thing I will do differently in the next lab:
```

Keep it short.

The goal is not a long report.

The goal is better thinking.

---

# Standard for Future Labs

From now on, do not say:

```text
It worked.
```

Say:

```text
It worked because this command produced this evidence.
```

Do not say:

```text
It failed.
```

Say:

```text
It failed at this layer, and this log line or command output proves it.
```

That is the habit.
