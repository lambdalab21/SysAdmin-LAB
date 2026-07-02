# SSH Connection Closed: Coached Follow-Up Lab

## Purpose

This lab is not mainly about fixing SSH.

It is about learning how to troubleshoot slowly and correctly:

```text
observe → quote evidence → classify the failure → test one assumption → change one thing → verify
```

The current symptom is:

```text
Connection to 192.168.0.107 closed.
```

The strongest server-side evidence so far is:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
pam_unix(sshd:session): session opened for user deploy by (uid=0)
error: PAM: pam_open_session(): Permission denied
```

and:

```text
PAM pam_parse: expecting return value; [...optimal]
pam_motd(sshd:session): unknown option: noupdate
pam_motd(sshd:session): unknown option: =/etc/ssh/motd
```

## Big coaching message

Slow down here.

Do not treat this as “SSH is broken.” That is too vague.

You already know several things work:

```text
network path works
port 22 is reachable
sshd is running
password authentication succeeds
PAM starts opening the session
PAM session setup fails
```

The next investigation is not random SSH troubleshooting.

The next investigation is:

```text
Why does the SSH PAM session stack fail during pam_open_session()?
```

---

# How to Work Through This Lab

## Before each command

Write this:

```text
I am about to run:

This command tests:

If my idea is correct, I expect:

If my idea is wrong, I expect:
```

If you cannot fill this in, stop. You are about to type without thinking.

## After each command

Write this:

```text
Exact output or exact log line:

What this proves:

What this does NOT prove:

Next narrow question:
```

Do not write only “it worked” or “it did not work.” That is not evidence.

---

# Part 1: Stop and Classify the Failure Correctly

## Look carefully at this line

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
```

Take time here.

This line contains four pieces of evidence:

| Evidence | Meaning |
|---|---|
| `Accepted password` | password authentication succeeded |
| `deploy` | the username was recognized |
| `192.168.0.107` | the client IP reached the server |
| `ssh2` | SSH protocol negotiation happened |

## Do not rush past this conclusion

This one line rules out several bad guesses:

```text
wrong password
wrong IP address
firewall blocking the original attempt
sshd not running
port 22 unreachable
```

## Write this answer

```text
Evidence line:

Authentication succeeded because:

This rules out:

This does not yet prove:
```

Expected thinking:

```text
It proves authentication succeeded.
It does not prove that the login shell started successfully.
```

---

# Part 2: Focus on the Failure Point

Now look at:

```text
pam_unix(sshd:session): session opened for user deploy by (uid=0)
error: PAM: pam_open_session(): Permission denied
```

Slow down here.

The word `session` matters.

PAM has different stages. Do not mix them up.

```text
auth      = prove who you are
account   = check whether the account is allowed
password  = change/manage password
session   = set up the login session after authentication
```

Your failure is not at `auth`.

Your failure is during `session`.

## Write this answer

```text
The failure occurs during which PAM stage?

The evidence is:

Why this is different from wrong password:

What files probably control this stage:
```

Expected direction:

```text
/etc/pam.d/sshd
/etc/pam.d/postlogin
/etc/pam.d/password-auth
/etc/pam.d/system-auth
```

---

# Part 3: Pay Careful Attention to the Parser Warning

Look carefully at:

```text
PAM pam_parse: expecting return value; [...optimal]
```

Do not skim this.

The suspicious word is:

```text
optimal
```

PAM control flags commonly include:

```text
required
requisite
sufficient
optional
include
substack
```

`optimal` is suspicious because it looks like a typo for:

```text
optional
```

## Coaching point

A one-letter typo in a PAM file can break logins.

This is why you do not copy configuration from random instructions without reading it.

## Find it

Run:

```bash
sudo grep -Rni 'optimal' /etc/pam.d
```

Then run:

```bash
sudo grep -Rni 'optional' /etc/pam.d
```

## Write this answer

```text
Command:

Exact file and line containing `optimal`:

Exact line:

What I think the typo should be:

Why I think so:
```

## Stop point

Do not edit yet.

First prove where the line is and what it says.

---

# Part 4: Pay Careful Attention to the `pam_motd` Warnings

Look carefully at:

```text
pam_motd(sshd:session): unknown option: noupdate
pam_motd(sshd:session): unknown option: =/etc/ssh/motd
```

These lines do not merely say “something is weird.”

They tell you:

```text
The module is pam_motd.so.
The PAM interface is session.
The options are not understood by this system's pam_motd.
```

## Find the line

Run:

```bash
sudo grep -Rni 'pam_motd' /etc/pam.d
```

Then run:

```bash
sudo grep -RniE 'noupdate|motd' /etc/pam.d
```

## Write this answer

```text
File:

Line number:

Exact line:

Interface:

Control flag:

Module:

Arguments:
```

Example structure:

```text
session  optional  pam_motd.so  argument1 argument2
```

## Slow down here

Do not just say “the line is wrong.”

Break the line into fields.

If you cannot identify the interface, control flag, module, and arguments, you are not ready to edit it.

---

# Part 5: Fix the Evidence-Capture Problem

You wrote that some redirected files were empty.

Before continuing, test whether redirection is working and whether you are looking in the right directory.

Run:

```bash
pwd
whoami
ls -ld ~/ssh-pam-lab
```

Now run:

```bash
echo TEST > ~/ssh-pam-lab/test-output.txt
cat ~/ssh-pam-lab/test-output.txt
```

Expected:

```text
TEST
```

Now test command output redirection:

```bash
sudo nl -ba /etc/pam.d/sshd > ~/ssh-pam-lab/sshd-pam-numbered.txt
ls -l ~/ssh-pam-lab/sshd-pam-numbered.txt
wc -l ~/ssh-pam-lab/sshd-pam-numbered.txt
sed -n '1,80p' ~/ssh-pam-lab/sshd-pam-numbered.txt
```

## If the file is empty

Run:

```bash
sudo nl -ba /etc/pam.d/sshd
```

If this prints to the screen but the redirected file is empty, you made a path or shell mistake.

Run:

```bash
ls -la ~/ssh-pam-lab
find ~/ssh-pam-lab -maxdepth 1 -type f -ls
```

## Important shell lesson

In this command:

```bash
sudo nl -ba /etc/pam.d/sshd > ~/ssh-pam-lab/sshd-pam-numbered.txt
```

`sudo` applies to `nl`, not to the `>` redirection.

The redirection is handled by your current shell.

Because the destination is in your home directory, that should be okay.

If redirecting into a root-owned directory, use:

```bash
sudo sh -c 'nl -ba /etc/pam.d/sshd > /root/sshd-pam-numbered.txt'
```

But for this lab, keep output under:

```text
~/ssh-pam-lab/
```

## Write this answer

```text
My current directory:

My current user:

Does simple redirection work?

Does `nl -ba /etc/pam.d/sshd` print to screen?

Does the saved file have more than 0 lines?

If not, what exact command did I type?
```

---

# Part 6: Compare with a Known-Good Control Before Editing

This is where you practice `急がば回れ`.

Do not edit based only on suspicion.

Use your control AlmaLinux VM.

On both `app01` and the control machine, run:

```bash
rpm -q almalinux-release openssh-server pam
```

On both machines, save:

```bash
sudo nl -ba /etc/pam.d/sshd > ~/sshd.pam.txt
sudo nl -ba /etc/pam.d/postlogin > ~/postlogin.pam.txt
sudo nl -ba /etc/pam.d/password-auth > ~/password-auth.pam.txt
sudo nl -ba /etc/pam.d/system-auth > ~/system-auth.pam.txt
```

Copy the files to the same machine and compare:

```bash
diff -u control-postlogin.pam.txt app01-postlogin.pam.txt
```

Also compare:

```bash
diff -u control-sshd.pam.txt app01-sshd.pam.txt
diff -u control-password-auth.pam.txt app01-password-auth.pam.txt
diff -u control-system-auth.pam.txt app01-system-auth.pam.txt
```

## Pay attention to differences involving

```text
pam_motd
optimal
optional
session
include
substack
```

## Write this answer

```text
The control machine differs from app01 in this file:

Exact differing line on app01:

Exact corresponding line on control:

Which log message points to this difference:

Why this difference could cause pam_open_session() to fail:
```

## Stop point

Do not edit until you can connect:

```text
log warning → exact file → exact line → known-good comparison → smallest edit
```

---

# Part 7: Decide the Smallest Safe Edit

Based on the current evidence, likely candidates are:

1. Change a misspelled control flag:

```text
session optimal pam_motd.so ...
```

to:

```text
session optional pam_motd.so ...
```

2. Remove or simplify invalid `pam_motd` arguments if the control machine shows no such arguments.

For example, if app01 has:

```text
session optional pam_motd.so noupdate=/etc/ssh/motd
```

but the control machine has:

```text
session optional pam_motd.so
```

then the smaller safer test may be to match the control machine.

## Before editing

Write:

```text
I will edit this file:

I will change this exact line:

From:

To:

Why this is the smallest change:

How I will undo it:
```

Then back up the exact file:

```bash
sudo cp -a /etc/pam.d/postlogin /etc/pam.d/postlogin.bak.$(date +%Y%m%d-%H%M%S)
```

Replace `/etc/pam.d/postlogin` with the actual file you will edit.

## Coaching warning

Do not edit `/etc/pam.d/sshd` if the suspicious line is actually in `/etc/pam.d/postlogin`.

Edit the file that contains the bad line.

---

# Part 8: Test Correctly After the Edit

Open a physical-console terminal and run:

```bash
sudo journalctl -f -u sshd.service
```

From the development machine:

```bash
ssh deploy@192.168.0.107
```

If it works, run:

```bash
whoami
hostname
pwd
exit
```

Then inspect logs:

```bash
sudo journalctl -u sshd.service --since "5 minutes ago"
sudo tail -n 50 /var/log/secure
```

## Pay attention to what disappeared

The fix is not proven only by “I logged in.”

Also check whether these disappeared:

```text
PAM pam_parse: expecting return value
pam_motd unknown option
pam_open_session(): Permission denied
```

## Write this answer

```text
After the edit, remote SSH login:

Did `Accepted password` appear?

Did `session opened` appear?

Did `pam_open_session(): Permission denied` appear?

Did `pam_motd unknown option` appear?

Final conclusion:
```

---

# Part 9: If the First Edit Does Not Fix It

Do not panic.

Do not start changing unrelated files.

Return to the evidence.

Run:

```bash
sudo journalctl -u sshd.service --since "10 minutes ago"
sudo tail -n 80 /var/log/secure
```

Ask:

```text
Are the exact same PAM warnings still present?
Did one warning disappear but another remain?
Did the failure line change?
Did the problem move to a different module?
```

If the `pam_motd` warning remains, you did not fix the line Nginx—no, stop—SSH is actually using.

That sentence was deliberate: do not let your brain autocomplete from the previous Nginx lab. This is SSH/PAM.

Search again:

```bash
sudo grep -RniE 'pam_motd|noupdate|motd|optimal|optional' /etc/pam.d
```

## Write this answer

```text
The remaining warning is:

The file and line that still match it are:

My previous edit did or did not affect that line because:

The next smallest edit is:
```

---

# Part 10: Anti-Rushing Checklist

Before declaring anything, answer every item.

## Evidence checklist

```text
[ ] I quoted the exact log line.
[ ] I identified whether the failure is before auth, during auth, or after auth.
[ ] I did not call authentication failed after seeing `Accepted password`.
[ ] I found the exact PAM file and line.
[ ] I compared it with the control machine.
[ ] I made a backup of the exact file before editing.
[ ] I made one small edit only.
[ ] I tested from a new SSH session.
[ ] I checked logs after testing.
[ ] I wrote what changed and what did not change.
```

## Thought-process checklist

```text
[ ] My hypothesis predicts an observable result.
[ ] My command tests that hypothesis.
[ ] My conclusion follows from the output.
[ ] I did not change unrelated systems.
[ ] I did not confuse firewall, SSH authentication, PAM session, and shell startup.
```

---

# Part 11: What You Should Learn From This Incident

This is the real lesson:

```text
The visible symptom was SSH closing.
The actual failing layer was PAM session setup.
The logs showed the layer.
The parser warning pointed to the kind of configuration mistake.
The control machine helped identify the exact bad line.
The fix should be the smallest correction to that line.
```

A weak troubleshooter sees:

```text
SSH is broken.
```

A stronger troubleshooter says:

```text
SSH password authentication succeeds, but PAM session setup fails.
The log points to a malformed pam_motd line in the session stack.
I will compare /etc/pam.d/postlogin with a known-good AlmaLinux system before editing.
```

That is the standard you are aiming for.

---

# Final Report Template

Complete this after the next attempt:

```text
Symptom:

Strongest log evidence:

Failure stage:

Wrong assumptions I almost made:

Exact suspicious PAM file:

Exact suspicious line:

Known-good control line:

Smallest edit made:

Backup file created:

Test command:

Post-fix logs:

Did SSH login work?

Did PAM warnings disappear?

Root cause:

One thing I learned about troubleshooting:
```
