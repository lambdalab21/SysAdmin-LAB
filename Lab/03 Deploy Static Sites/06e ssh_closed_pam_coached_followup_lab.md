#deployment #pam #LAB 

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

| Evidence            | Meaning                           |
| ------------------- | --------------------------------- |
| `Accepted password` | password authentication succeeded |
| `deploy`            | the username was recognized       |
| `192.168.0.107`     | the client IP reached the server  |
| `ssh2`              | SSH protocol negotiation happened |

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
Evidence line:Accepted password for deploy from 192.168.0.107 port 38652 ssh2

Authentication succeeded because: SSH accepted the password and PAM authentication passed. 

This rules out: Incorrect passwords, unreachable server, firewall block, sshd down.

This does not yet prove: That the session fully opened or that the shell had started. 
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

Your failure is during session

Your failure is during `session`.

## Write this answer

```text
The failure occurs during which PAM stage? Session. 

The evidence is: `pam_unix(sshd:session): session opened` followed by `error: PAM: pam_open_session(): Permission denied.`

Why this is different from wrong password: Password authorization succeeded; but the failure is after auth during environment setup. 

What files probably control this stage:
/etc/pam.d/sshd
/etc/pam.d/postlogin
/etc/pam.d/password-auth
/etc/pam.d/system-auth
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
Command: sudo grep -Rni 'optimal' /etc/pam.d (and the optional one).
Exact file and line containing `optimal`:
/etc/pam.d/sshd:15:session
/etc/pam.d/sshd.bak.20260613
/etc/pam.d/sshd.bak.Y0613-144700:15:session
```

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
File: /etc/pam.d/sshd
/etc/pam.d/sshd:15:session
/etc/pam.d/sshd:16:session
/etc/pam.d/sshd.bak.20260613-130Z12:15:session
/etc/pam.d/sshd.bak.20260613-130Z12:16:session
/etcpam.d/sshd.bak.Y0613-144700:16:session
```

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
(First line returns Permission Denied regardless of sudo used or not.)

Expected:

```text
TEST
```

Now test command output redirection:

```bash
sudo nl -ba /etc/pam.d/sshd > ~/ssh-pam-lab/sshd-pam-numbered.txt
(Permission Denied)
ls -l ~/ssh-pam-lab/sshd-pam-numbered.txt 
(-rw-r--r--. 1 root root 0 Jun 13 12:26 /home/user/ssh-pam-lab/sshd-pam-numbered.txt)
wc -l ~/ssh-pam-lab/sshd-pam-numbered.txt
(0 /home/user/ssh-pam-lab/sshd-pam-numbered.txt)
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
My current directory: /home/user

My current user: ifujimoto

Does simple redirection work? No. Files empty despite no error.

Does `nl -ba /etc/pam.d/sshd` print to screen? Yes. 

Does the saved file have more than 0 lines? No. 
```

---

# Part 6: Compare with a Known-Good Control Before Editing

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
The control machine differs from app01 in this file: /etc/pam.d/sshd

Exact differing line on app01: session optimal pam_motd.so noupdate =/etc/ssh/motd.

Exact corresponding line on control: Session optional pam_motd.so

Which log message points to this difference: pam_parse: expecting return value; [...optimal] and pam_motd ... unknown option: noupdate

Why this difference could cause pam_open_session() to fail: The parser fails invalid control flag "optimal". The whole session module returns Permission Denied. 
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
I will edit this file: /etc/pam.d/sshd

I will change this exact line: Line 15, the one with optimal.

From: session optimal pam_motd.so noupdate=/etc/ssh/motd

To: session optional pam_motd.so

Why this is the smallest change: Only fixes clear typo and removes invalid arguments. Matches control machine. 

How I will undo it: Restore from the timestamped .bak
```
**^The above worked. SSH to 192.168.0.107 works now.** 

Then back up the exact file:

```bash
sudo cp -a /etc/pam.d/postlogin /etc/pam.d/postlogin.bak.$(date +%Y%m%d-%H%M%S)
```

Replace `/etc/pam.d/postlogin` with the actual file you will edit.

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

Did `Accepted password` appear? Yes. 

Did `session opened` appear? Yes. 

Did `pam_open_session(): Permission denied` appear? No.

Did `pam_motd unknown option` appear? No. 

Final conclusion: Fix successful, SSH works. 
```

---

# Part 9: If the First Edit Does Not Fix It

**Skipped since it did fix it.** 
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
Symptom: SSH Connection Closed after Password 

Strongest log evidence: Accepted password + pam_unix session opened + pam_open_session(): Permission denied.

Failure stage:PAM Session

Wrong assumptions I almost made: Firewall, password, routing, and user-specific details. 

Exact suspicious PAM file:etc/pam.d/sshd

Exact suspicious line: Session Optimal pam_motd.so noupdate =/etc/ssh/motd

Known-good control line:Session optional pam_motd.so

Smallest edit made: Changed "Optimal" to "Optional" and removed bad arguments. 

Backup file created: /etc/pam.d/sshd.bak

Test command:ssh deploy@192.168.0.107

Post-fix logs:Clean

Did SSH login work? Yes. 

Did PAM warnings disappear? Yes. 

Root cause: Try "optimal" instead of "optional" + Ubuntu-style pam_motd options on AlmaLinux
```
