The guided lab did not fail. It produced a useful diagnosis boundary.

He has already proven that this is **not primarily a firewall, routing, port, or password-authentication problem**. The logs show:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
pam_unix(sshd:session): session opened for user deploy by (uid=0)
error: PAM: pam_open_session(): Permission denied
```

They also show suspicious PAM parser and `pam_motd` warnings:

```text
PAM pam_parse: expecting return value; [...optimal]
pam_motd(sshd:session): unknown option: noupdate
pam_motd(sshd:session): unknown option: =/etc/ssh/motd
```

Those lines narrow the investigation to the SSH PAM session stack, likely `/etc/pam.d/sshd` or a PAM file included from it. They do **not yet prove the exact broken line**, so he should inspect and compare before editing.

This is exactly where 急がば回れ matters.

# 1. Improvements he should make in his lab work

## A. Read log lines literally

He missed evidence that was directly in front of him.

He wrote:

> Did the log mention the client IP address? No.

But the log contains:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
```

The client IP is:

```text
192.168.0.107
```

He wrote:

> Did the log mention the username? No.

But the same line contains:

```text
for deploy
```

The username is:

```text
deploy
```

This is the first habit to correct:

> Do not answer from memory. Quote the exact log line and point to the exact words that support the answer.

## B. Distinguish authentication from session setup

He classified the failure as authentication failure because he saw:

```text
Permission denied
```

That was too fast.

The earlier line says:

```text
Accepted password for deploy
```

That means password authentication succeeded.

The later failure is:

```text
pam_open_session(): Permission denied
```

PAM separates authentication from session management. `pam_open_session()` sets up a session for a user who has already authenticated successfully. ([man7.org](https://man7.org/linux/man-pages/man3/pam.3.html?utm_source=chatgpt.com "pam(3) - Linux manual page"))

The correct sequence is:

```text
TCP connection succeeded
→ sshd answered
→ password authentication succeeded
→ PAM began session setup
→ PAM session setup failed
→ SSH closed the connection
```

He should have followed the branch:

```text
Authentication succeeded, but session closed.
```

not:

```text
Authentication failed.
```

## C. Write a falsifiable hypothesis

He wrote:

```text
Initial hypothesis: Didn't work.
```

That is not a hypothesis. It cannot be tested.

A good hypothesis would be:

```text
Password authentication succeeds, but a PAM session module used by sshd
rejects remote session setup. Because multiple users fail remotely while
local login works, the issue is probably system-wide SSH PAM session
configuration rather than a deploy-specific account problem.
```

A useful hypothesis must predict what another test will show.

## D. Use commands that answer the actual question

He wrote that the `takashi` account was locked because an incorrect password returned permission denied.

That does not prove the account is locked. It proves only that an incorrect password was rejected.

Use:

```bash
sudo passwd -S takashi
sudo chage -l takashi
```

Questions must be paired with evidence-producing commands.

|Question|Better command|
|---|---|
|Does the user exist?|`getent passwd takashi`|
|Is the account locked?|`sudo passwd -S takashi`|
|Is the account expired?|`sudo chage -l takashi`|
|What shell is assigned?|`getent passwd deploy \| cut -d: -f7`|
|Did password authentication succeed?|read `Accepted password` log line|
|Did session setup fail?|read `pam_open_session()` log line|

## E. Do not use `echo $SHELL` as the main account check

He used:

```bash
echo $SHELL
```

That reports the current shell environment variable. It may be useful, but it is not the authoritative account record.

Use:

```bash
getent passwd deploy
```

or:

```bash
getent passwd deploy | cut -d: -f7
```

The final field from the account database is the configured login shell.

## F. Do not contradict evidence collected earlier

Later, he wrote:

> Did TCP connection succeed? No.

But this cannot be correct if the server logged:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
```

TCP connection, SSH handshake, and password authentication must all have succeeded before the server could log that line.

This is a major troubleshooting discipline:

> Every new conclusion must remain compatible with earlier evidence.

## G. Use the debug command correctly

He used:

```bash
ssh -vvv -E 192.168.0.107-ssh-debug.log deploy@192.168.0.107.xxx
```

There are two issues:

1. The destination appears malformed:
    

```text
192.168.0.107.xxx
```

2. `-E logfile` sends debug output to the file, so little or nothing may appear on the terminal.
    

Use:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.107
```

Then read:

```bash
tail -n 60 app01-ssh-debug.log
```

The OpenSSH client documents `-v` as verbose diagnostic output for connection, authentication, and configuration troubleshooting. ([man7.org](https://man7.org/linux/man-pages/man1/ssh.1.html?utm_source=chatgpt.com "ssh(1) - Linux manual page"))

## H. Do not change unrelated settings just because the command succeeds

He wrote:

```bash
sudo firewall-cmd --remove-service=ssh
```

and observed:

```text
success
```

That command succeeded in changing the firewall configuration. It did not fix the original problem because the original problem was occurring **after** the connection reached `sshd`.

Worse, removing the firewall rule can create a new problem and obscure the old one.

The correct question before running a command is:

> What hypothesis does this command test?

If the answer is unclear, do not run it.

# 2. Comments and responses for him

Here is the feedback I would give him directly.

## What he did well

He correctly:

- reproduced the problem while following `sshd` logs;
    
- captured the final meaningful error:
    
    ```text
    PAM: pam_open_session(): Permission denied
    ```
    
- tested a second account;
    
- tested local login using:
    
    ```bash
    sudo -iu deploy
    ```
    
- verified that `/etc/nologin` does not exist;
    
- recognized that the remote session opens and closes immediately;
    
- kept investigating instead of blindly reinstalling the operating system.
    

Those are good instincts.

## What he should improve next

He needs to slow down at the classification step.

The log line:

```text
Accepted password for deploy
```

means authentication succeeded.

The line:

```text
session opened for user deploy
```

means PAM started session handling.

The line:

```text
pam_open_session(): Permission denied
```

means session setup failed.

The suspicious lines immediately before it mention PAM parsing and unsupported `pam_motd` options. The next job is not to experiment with the firewall. The next job is to inspect the PAM configuration used by `sshd`.

Linux PAM configuration files list modules and describe what PAM should do if a module succeeds or fails. The standard format is:

```text
module_interface  control_flag  module_name  module_arguments
```

For example:

```text
session  optional  pam_motd.so
```

Red Hat documents this PAM configuration structure, including the distinction between interfaces such as `auth`, `account`, and `session`. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/system-level_authentication_guide/pam_configuration_files?utm_source=chatgpt.com "10.2. About PAM Configuration Files"))

The `pam_motd` module displays a message of the day after successful login. The warnings mentioning unsupported options suggest that at least one PAM configuration line deserves inspection. ([man7.org](https://man7.org/linux/man-pages/man8/pam_motd.8.html?utm_source=chatgpt.com "pam_motd(8) - Linux manual page"))

Do not immediately delete the suspicious lines. First find them, compare them to a known-good AlmaLinux configuration, and explain what is wrong.

# 3. Follow-up lab: isolate the PAM session failure

# Follow-Up Lab: Isolate the SSH PAM Session Failure

## Goal

Determine why remote SSH login closes after password authentication succeeds.

Do not guess. Use logs, configuration inspection, package verification, and a known-good control machine.

## Current evidence

The server logs show:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
pam_unix(sshd:session): session opened for user deploy by (uid=0)
error: PAM: pam_open_session(): Permission denied
```

The logs also show:

```text
PAM pam_parse: expecting return value; [...optimal]
pam_motd(sshd:session): unknown option: noupdate
pam_motd(sshd:session): unknown option: =/etc/ssh/motd
```

## Current working hypothesis

Write this in your lab journal:

```text
Password authentication succeeds, but a PAM session module used by sshd
fails during remote session setup.

Because multiple users fail remotely and local login works, I suspect a
system-wide SSH PAM session configuration problem rather than a user-specific
account problem, firewall problem, or TCP problem.
```

---

# Part 1: Preserve Evidence Before Changing Anything

At the physical console of `app01`:

```bash
mkdir -p ~/ssh-pam-lab
```

Save recent SSH logs:

```bash
sudo journalctl -u sshd.service --since "30 minutes ago" \
  > ~/ssh-pam-lab/sshd-journal-before.txt
```

```bash
sudo cp -a /var/log/secure ~/ssh-pam-lab/secure-before.txt
```

Back up PAM configuration:

```bash
sudo cp -a /etc/pam.d ~/ssh-pam-lab/pam.d-before
```

Back up SSH configuration:

```bash
sudo cp -a /etc/ssh ~/ssh-pam-lab/ssh-before
```

## Questions

1. Why make backups before editing?
    
2. Which files contain evidence?
    
3. Which files control behavior?
    
4. How are evidence files different from configuration files?
    

---

# Part 2: Reproduce Once More with a Timestamp

At the physical console, Terminal A:

```bash
sudo journalctl -f -u sshd.service
```

At the physical console, Terminal B:

```bash
sudo tail -f /var/log/secure
```

From the development machine:

```bash
date
ssh deploy@192.168.0.107
```

Record the exact timestamp and all new log lines.

## Questions

1. Does the server log `Accepted password`?
    
2. Does it log `session opened`?
    
3. Does it log `pam_open_session(): Permission denied`?
    
4. Are the `pam_motd` warnings reproducible?
    
5. Does the parser warning appear on every SSH attempt?
    

---

# Part 3: Inspect the PAM File Used by SSH

At the physical console:

```bash
sudo nl -ba /etc/pam.d/sshd
```

The `nl -ba` command prints every line with a line number.

Save it:

```bash
sudo nl -ba /etc/pam.d/sshd \
  > ~/ssh-pam-lab/sshd-pam-numbered.txt
```

Search all PAM configuration files for the suspicious terms from the logs:

```bash
sudo grep -RniE \
  'pam_motd|noupdate|motd|optimal|optional' \
  /etc/pam.d
```

Save the output:

```bash
sudo grep -RniE \
  'pam_motd|noupdate|motd|optimal|optional' \
  /etc/pam.d \
  > ~/ssh-pam-lab/pam-suspicious-lines.txt
```

## Questions

1. Which file contains `pam_motd.so`?
    
2. Which line contains `noupdate`?
    
3. Which line contains `motd`?
    
4. Does any line contain `optimal` instead of `optional`?
    
5. Does the log warning match a configuration line exactly?
    
6. Was this line added manually?
    

## Important

Do not edit yet.

First write:

```text
Suspicious file:

Suspicious line number:

Exact line:

Why it appears suspicious:

Log line that points to it:
```

---

# Part 4: Learn the PAM Line Structure

Read:

```bash
man pam.d
```

If that page is unavailable:

```bash
man pam.conf
```

A PAM line generally has this structure:

```text
module_interface  control_flag  module_name  module_arguments
```

Example:

```text
session  optional  pam_motd.so
```

Label the fields in every suspicious line.

Write:

```text
Interface:

Control flag:

Module:

Arguments:
```

## Questions

1. Is the interface `auth`, `account`, `password`, or `session`?
    
2. Is the control flag spelled correctly?
    
3. Is `optional` written correctly?
    
4. Are square-bracket control expressions complete?
    
5. Are module arguments valid for this version of `pam_motd`?
    
6. Is the syntax from Ubuntu documentation being used on AlmaLinux?
    

## Lesson

A configuration copied from another distribution may not be valid here.

Do not assume that a Linux tutorial applies unchanged to every distribution.

---

# Part 5: Inspect Included PAM Files

The SSH PAM file may include other PAM stacks.

Run:

```bash
sudo nl -ba /etc/pam.d/sshd
```

Look for lines containing:

```text
include
substack
```

Inspect likely files if referenced:

```bash
sudo nl -ba /etc/pam.d/password-auth
sudo nl -ba /etc/pam.d/system-auth
sudo nl -ba /etc/pam.d/postlogin
```

Search again:

```bash
sudo grep -RniE \
  'pam_motd|noupdate|motd|optimal|optional' \
  /etc/pam.d
```

## Questions

1. Does `/etc/pam.d/sshd` contain the suspicious line directly?
    
2. Does it inherit the line from another file?
    
3. Is the failure SSH-specific?
    
4. Could the same broken PAM file affect other login methods?
    

---

# Part 6: Verify Package-Managed Configuration

Identify which package owns the SSH PAM file:

```bash
rpm -qf /etc/pam.d/sshd
```

Verify the installed OpenSSH server package:

```bash
rpm -V openssh-server
```

Record the output:

```bash
rpm -V openssh-server \
  > ~/ssh-pam-lab/openssh-server-rpm-verify.txt
```

Check the authentication profile:

```bash
authselect current
```

```bash
authselect check
```

Save:

```bash
authselect current > ~/ssh-pam-lab/authselect-current.txt
authselect check   > ~/ssh-pam-lab/authselect-check.txt
```

## Questions

1. Which package owns `/etc/pam.d/sshd`?
    
2. Does RPM verification report that the file differs from the packaged version?
    
3. Does `authselect check` report a valid configuration?
    
4. Are `system-auth` or `password-auth` generated by `authselect`?
    
5. Do any files say:
    
    ```text
    Do not modify this file manually
    ```
    

## Important

On RHEL-style systems, some authentication configuration files are generated by `authselect`. Do not manually edit generated files without understanding how they are managed.

---

# Part 7: Create a Known-Good Control Machine

Use Proxmox.

Create a temporary AlmaLinux VM using the same major version as `app01`.

Name it:

```text
alma-control
```

Install OpenSSH server if necessary:

```bash
sudo dnf install openssh-server
```

On both machines, record versions:

```bash
rpm -q almalinux-release
rpm -q openssh-server
rpm -q pam
```

On `alma-control`, save:

```bash
sudo nl -ba /etc/pam.d/sshd
sudo nl -ba /etc/pam.d/password-auth
sudo nl -ba /etc/pam.d/system-auth
sudo nl -ba /etc/pam.d/postlogin
```

Compare the suspicious file line by line.

You may copy the files to the development machine and run:

```bash
diff -u alma-control-sshd app01-sshd
```

## Questions

1. Which lines differ?
    
2. Are the suspicious `pam_motd` options present on the control machine?
    
3. Is `optimal` present on the control machine?
    
4. Does the control machine allow SSH login?
    
5. Which difference most plausibly explains the log warnings?
    

## Lesson

A known-good control case is stronger than guessing.

---

# Part 8: Write a Specific Hypothesis Before Editing

Complete:

```text
The likely root cause is:

The evidence is:

The file to edit is:

The exact line to change is:

The control machine shows:

The smallest safe change is:

How I will undo the change:
```

Do not proceed until each field is complete.

---

# Part 9: Make the Smallest Correct Change

At the physical console, make another backup of only the file you will edit:

```bash
sudo cp -a /etc/pam.d/sshd \
  /etc/pam.d/sshd.bak.$(date +%Y%m%d-%H%M%S)
```

If the suspicious line is in another file, back up that file instead.

Edit only the line justified by evidence:

```bash
sudo vi /etc/pam.d/sshd
```

Do not copy an entire configuration stack from the internet.

Do not overwrite unrelated files.

Do not disable PAM.

Do not disable SELinux.

---

# Part 10: Test from a New Remote Terminal

Keep the physical-console session open.

At the physical console, watch:

```bash
sudo journalctl -f -u sshd.service
```

From the development machine:

```bash
ssh deploy@192.168.0.107
```

If login works, run:

```bash
whoami
hostname
pwd
```

Exit:

```bash
exit
```

Inspect logs:

```bash
sudo journalctl -u sshd.service --since "5 minutes ago"
```

```bash
sudo tail -n 50 /var/log/secure
```

## Questions

1. Did the `pam_motd` warnings disappear?
    
2. Did `pam_open_session()` succeed?
    
3. Did the SSH shell remain open?
    
4. Did both `deploy` and `takashi` work?
    
5. What evidence proves the fix?
    

---

# Part 11: Test the Client Debug Command Correctly

From the development machine:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.107
```

Read the saved output:

```bash
tail -n 60 app01-ssh-debug.log
```

## Questions

1. Why did debug output go to a file?
    
2. Which line indicates successful authentication?
    
3. Which line indicates that a session was requested?
    
4. Which line indicates normal session closure after typing `exit`?
    
5. How does the successful debug output differ from the earlier failed attempt?
    

---

# Part 12: Write the Final Incident Report

Complete:

```text
Symptom:

First useful log line:

Authentication succeeded or failed?

Session setup succeeded or failed?

Incorrect initial assumption:

Evidence that disproved it:

Suspicious PAM warnings:

Configuration file inspected:

Difference from known-good control:

Root cause:

Smallest change made:

Verification command:

Verification log lines:

How to undo the change:

What I would do differently next time:
```

---

# Completion Checkpoint

Explain:

1. Why did `Accepted password` prove that authentication succeeded?
    
2. Why did `pam_open_session()` move the investigation to PAM session handling?
    
3. Why was the firewall not the main suspect?
    
4. Why did testing a second user matter?
    
5. Why did successful local login matter?
    
6. Why was `echo $SHELL` weaker than `getent passwd deploy`?
    
7. Why did `ssh -E logfile` appear silent?
    
8. Why was `192.168.0.107.xxx` an invalid test target?
    
9. What is the structure of a PAM configuration line?
    
10. What is the difference between `auth` and `session` in PAM?
    
11. Why compare against a known-good AlmaLinux control VM?
    
12. Why make the smallest possible edit?
    
13. Why keep the physical-console session open?
    
14. What evidence proved the final fix?
    

# Likely direction, without pretending certainty

Based on his logs, the most likely issue is a malformed or incompatible PAM session configuration line, probably involving `pam_motd`. The clues are unusually specific:

```text
pam_parse: expecting return value; [...optimal]
pam_motd(sshd:session): unknown option: noupdate
pam_motd(sshd:session): unknown option: =/etc/ssh/motd
pam_open_session(): Permission denied
```

`pam_motd` is a PAM module that displays login messages after successful authentication. Its accepted behavior and options depend on the installed implementation. ([man7.org](https://man7.org/linux/man-pages/man8/pam_motd.8.html?utm_source=chatgpt.com "pam_motd(8) - Linux manual page"))

Do not tell him to delete the `pam_motd` line immediately.

Make him prove:

1. where the line came from;
    
2. how it differs from the AlmaLinux default;
    
3. whether correcting that one line removes both the warning and the login failure.
    

That is the difference between fixing a machine and learning infrastructure.