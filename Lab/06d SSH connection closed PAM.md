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
^ Error: `File is neither a device node, nor regular file, nor executable: /home/ifujimoto/ssh`
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

1. Why make backups before editing? In case something goes wrong so we can go back to when/where it worked well.
    
2. Which files control behavior? Plain-text configuration files. 
    
3. How are evidence files different from configuration files?
    Configuration files decide how an application behaves while an evidence file records what users or applications perform.

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
ssh deploy@192.168.0.107
```

Record the exact timestamp and all new log lines.

## Questions

1. Does the server log `Accepted password`? Yes.
    
2. Does it log `session opened`? Yes. 
    
3. Does it log `pam_open_session(): Permission denied`? Yes. 
    
4. Are the `pam_motd` warnings reproducible? Yes. 
    
5. Does the parser warning appear on every SSH attempt? Yes. 
    

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
^ Even after saving the output, when I cat the file it doesn't return anything. Tried many times.
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
^ Even after saving the output, when I cat the file it doesn't return anything. Tried many times.
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
Unable to read any file other than secure-before even after redoing all the steps a few times. There are no errors in the 'save to file' steps, yet typing out `sudo cat file.txt` for any of the files other than secure-before.txt returns nothing. 

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

1. Does `/etc/pam.d/sshd` contain the suspicious line directly? It included many files that are labeled either 'substack' or 'include'. Those files are: 
	1. password-auth
	2. postlogin
	There are replicas of these files twice.
	
2. Is the failure SSH-specific?
    
3. Could the same broken PAM file affect other login methods?
    

---

# Part 6: Verify Package-Managed Configuration

Identify which package owns the SSH PAM file:

```bash
rpm -qf /etc/pam.d/sshd
```
^ openssh-server-8.0p1-29.e18_10.x86_64

Verify the installed OpenSSH server package:

```bash
rpm -V openssh-server
```
^ 
S.5....T. c /etc/pam.d/sshd
S.?....T. c /etc/sshd/sshd_config
..?...... c /etc/sysconfig/sshd
Record the output:

```bash
rpm -V openssh-server \
  > ~/ssh-pam-lab/openssh-server-rpm-verify.txt
```

Check the authentication profile:

```bash
authselect current
```
^ No existing configuration detected.
```bash
authselect check
```
^ System was not configured with authselect.
Save:

```bash
authselect current > ~/ssh-pam-lab/authselect-current.txt
authselect check   > ~/ssh-pam-lab/authselect-check.txt
```

## Questions

1. Does RPM verification report that the file differs from the packaged version? Yes.
    
2. Does `authselect check` report a valid configuration? Yes. 
    
3. Are `system-auth` or `password-auth` generated by `authselect`? Yes. 
     
4. Do any files say:
    
    ```text
    Do not modify this file manually
    ```
    No. 

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
Almalinux:
1.) almalinux-release-8.10-1.el8.x86_64
2.) openssh-server-8.0p1-29.el8_10.x86_64
3.) pam-1.3.1-33.el8.x86_64


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

1. Did the `pam_motd` warnings disappear? No.
    
2. Did `pam_open_session()` succeed? No. 
    
3. Did the SSH shell remain open? No. 
    
4. Did both `deploy` and `takashi` work? No. 

---

# Part 11: Test the Client Debug Command Correctly

From the development machine:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.107
```
^Connection Closed. 
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