
Do not start by editing configuration files. Capture evidence first.

The message:

```text
Connection to 192.168.0.xxx closed.
```

is not a diagnosis. It is different from:

```text
Connection timed out
Connection refused
Permission denied
```

A closed SSH connection can happen at several stages: before authentication, after authentication but before a shell starts, or immediately after the shell starts and exits. The SSH client’s `-v` option prints debugging messages specifically for connection, authentication, and configuration troubleshooting; up to three `v` flags increase detail. On the server, `sshd -t` checks configuration validity, while `sshd -T` displays the effective configuration and can account for `Match` rules. ([man.openbsd.org](https://man.openbsd.org/ssh.1 "ssh(1) - OpenBSD manual pages"))

Because he has physical-console access, this is a safe opportunity to learn systematic troubleshooting.

# SSH Troubleshooting Lab: Why Does `app01` Close the Connection?

## Scenario

You previously logged in remotely to the AlmaLinux server:

```text
app01
192.168.0.xxx
```

Now this command:

```bash
ssh deploy@192.168.0.xxx
```

or:

```bash
ssh your-user@192.168.0.xxx
```

returns:

```text
Connection to 192.168.0.xxx closed.
```

You can still log in directly using the physical keyboard and monitor.

Your task is not merely to restore SSH access.

Your task is to identify:

1. Which layers are working?
    
2. At which layer does the failure occur?
    
3. What evidence supports that conclusion?
    
4. What is the smallest correct fix?
    
5. How can you verify the fix safely?
    

---

# Ground Rules

1. Do not disable the firewall.
    
2. Do not disable SELinux.
    
3. Do not use `chmod -R 777`.
    
4. Do not randomly edit `/etc/ssh/sshd_config`.
    
5. Before editing any configuration file, make a backup.
    
6. After changing SSH configuration, validate it before reloading the service.
    
7. Keep the physical-console session open until a new remote SSH login succeeds.
    
8. Write down the output of each important command.
    

---

# Mental Model

An SSH login requires several layers to work:

```text
SSH client
   ↓
IP address and network path
   ↓
TCP port 22
   ↓
firewalld rule
   ↓
sshd process listening
   ↓
SSH protocol handshake
   ↓
user authentication
   ↓
account policy
   ↓
session setup
   ↓
login shell
```

Troubleshoot from the outside inward.

Do not jump directly to the layer you happen to understand best.

---

# Part 1: Capture the Exact Client-Side Failure

On the development machine, run:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.xxx
```

If a different user is failing, replace `deploy`.

Read the last 40 lines:

```bash
tail -n 40 app01-ssh-debug.log
```

## Questions

1. Did the client establish a TCP connection?
    
2. Did it exchange SSH version strings?
    
3. Did host-key negotiation occur?
    
4. Did authentication succeed?
    
5. Did the server close the connection before or after authentication?
    
6. What are the last five meaningful lines?
    

Record:

```text
Exact command:

Last meaningful client-side lines:

Did TCP connection succeed?

Did authentication succeed?

Did the session close before or after authentication?
```

## Interpretation Guide

### Case A: Timeout

```text
Connection timed out
```

Think:

```text
wrong IP
network path
machine down
firewall silently dropping traffic
```

### Case B: Refused

```text
Connection refused
```

Think:

```text
machine reachable
but no service listening on the requested port
```

### Case C: Authentication rejected

```text
Permission denied (publickey,password)
```

Think:

```text
sshd is reachable
SSH protocol handshake works
authentication failed
```

### Case D: Authenticated, then closed

Look for something similar to:

```text
Authenticated to 192.168.0.xxx
...
Connection to 192.168.0.xxx closed.
```

Think:

```text
network works
firewall works
sshd works
authentication works

investigate:
account policy
login shell
shell startup file
ForceCommand
Match block
authorized_keys restrictions
/etc/nologin
```

Do not treat all four cases as the same problem.

---

# Part 2: Watch Server Logs During a Login Attempt

At the physical console on `app01`, open one terminal and run:

```bash
sudo journalctl -fu sshd
```

Leave it running.

From the development machine, attempt login again:

```bash
ssh -vvv deploy@192.168.0.xxx
```

Watch the server terminal.

Also inspect recent messages afterward:

```bash
sudo journalctl -u sshd --since "10 minutes ago"
```

On AlmaLinux, also inspect authentication-related logs:

```bash
sudo tail -n 80 /var/log/secure
```

## Questions

1. Does the server receive the connection?
    
2. Does it identify the username?
    
3. Does authentication succeed?
    
4. Does it report an invalid shell?
    
5. Does it report an expired or locked account?
    
6. Does it report a PAM error?
    
7. Does it report a session opening and immediate closing?
    
8. Does it report a configuration restriction?
    

Record the exact error message.

Do not paraphrase it.

---

# Part 3: Confirm That `app01` Has the Expected IP Address

At the physical console:

```bash
hostname
hostname -I
ip addr
ip route
```

From the development machine:

```bash
ping -c 4 192.168.0.xxx
```

## Questions

1. Is `192.168.0.xxx` still assigned to `app01`?
    
2. Is the development machine connecting to the correct machine?
    
3. Did DHCP assign a different IP address?
    
4. Is the hostname `app01` resolving to the expected address?
    

Test:

```bash
getent hosts app01
```

## Lesson

A remembered IP address is not evidence.

Verify the address currently assigned to the machine.

---

# Part 4: Confirm That the SSH Service Is Running

At the physical console:

```bash
systemctl status sshd
```

Check whether it starts automatically after reboot:

```bash
systemctl is-enabled sshd
```

Check the process:

```bash
ps aux | grep '[s]shd'
```

Check the listening socket:

```bash
sudo ss -ltnp | grep ':22'
```

Expected general pattern:

```text
LISTEN ... 0.0.0.0:22 ...
```

or:

```text
LISTEN ... [::]:22 ...
```

## Questions

1. Is `sshd` active?
    
2. Is it enabled?
    
3. Is it listening on TCP port 22?
    
4. Is it listening on all interfaces or only one address?
    
5. Is another service listening on port 22?
    

## Important distinction

These statements are not equivalent:

```text
sshd package is installed
sshd process is running
sshd is listening on port 22
port 22 is allowed through the firewall
remote login succeeds
```

Each statement requires separate evidence.

---

# Part 5: Validate the SSH Server Configuration

Before editing anything, run:

```bash
sudo sshd -t
```

If there is no output, the syntax and host-key sanity checks passed.

Display the effective configuration:

```bash
sudo sshd -T | less
```

Search inside `less`:

```text
/port
/listenaddress
/passwordauthentication
/pubkeyauthentication
/permitrootlogin
/allowusers
/denyusers
/allowgroups
/denygroups
/forcecommand
/permittty
/chrootdirectory
/maxsessions
```

Quit:

```text
q
```

## Questions

1. Is the configuration syntactically valid?
    
2. Which port is configured?
    
3. Which addresses does `sshd` listen on?
    
4. Is public-key authentication enabled?
    
5. Is password authentication enabled?
    
6. Are users or groups explicitly allowed or denied?
    
7. Is a forced command configured?
    
8. Is TTY allocation permitted?
    
9. Is a chroot configured?
    
10. Is the maximum number of sessions unusual?
    

---

# Part 6: Inspect Firewall State

At the physical console:

```bash
sudo firewall-cmd --state
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all
```

If necessary, explicitly inspect the active zone:

```bash
sudo firewall-cmd --zone=public --list-services
sudo firewall-cmd --zone=public --list-ports
```

Look for:

```text
ssh
```

From the development machine:

```bash
nc -vz 192.168.0.xxx 22
```

If `nc` is unavailable:

```bash
telnet 192.168.0.xxx 22
```

A successful raw connection may display an SSH banner such as:

```text
SSH-2.0-OpenSSH_...
```

## Questions

1. Is the `ssh` service allowed in the active firewall zone?
    
2. Is port 22 reachable from the client?
    
3. Does the firewall explain the observed symptom?
    
4. If the client receives `Connection closed` after authentication, is the firewall still the most likely cause?
    

## Lesson

A firewall usually controls whether traffic reaches `sshd`.

It does not normally explain why a successfully authenticated user receives an immediate shell exit.

---

# Part 7: Determine Whether the Problem Affects One User or Every User

From the development machine, try at least two accounts:

```bash
ssh deploy@192.168.0.xxx
ssh your-admin-user@192.168.0.xxx
```

At the physical console, test local login behavior:

```bash
sudo -iu deploy
```

Then exit:

```bash
exit
```

Try:

```bash
sudo -iu your-admin-user
```

## Interpretation

### Only `deploy` fails

Focus on:

```text
deploy account
deploy login shell
deploy home directory
deploy startup files
deploy authorized_keys
Match User deploy configuration
```

### Every remote user fails, but local login works

Focus on:

```text
sshd configuration
global SSH restrictions
PAM
/etc/nologin
SSH-specific startup behavior
```

### Every local and remote login fails

Focus on:

```text
account database
shell path
filesystem permissions
PAM
system-wide startup files
```

## Questions

1. Is the problem user-specific?
    
2. Is it remote-login-specific?
    
3. Is it shell-specific?
    
4. What changed your hypothesis?
    

---

# Part 8: Inspect the User Account

At the physical console:

```bash
getent passwd deploy
id deploy
sudo passwd -S deploy
sudo chage -l deploy
```

Example output from `getent passwd`:

```text
deploy:x:1002:1002::/home/deploy:/bin/bash
```

The fields include:

```text
username
UID
GID
home directory
login shell
```

Inspect the shell:

```bash
getent passwd deploy | cut -d: -f7
ls -l /bin/bash
```

Check whether the shell is listed as valid:

```bash
cat /etc/shells
```

Look for problematic shells such as:

```text
/sbin/nologin
/bin/false
```

Inspect the home directory:

```bash
ls -ld /home/deploy
namei -l /home/deploy
```

## Questions

1. Does the user exist?
    
2. Is the account locked?
    
3. Is the account expired?
    
4. Does the home directory exist?
    
5. What shell is configured?
    
6. Does that shell exist?
    
7. Is the shell executable?
    
8. Is the shell intended to permit interactive login?
    

## Useful experiment

Temporarily create a known-good test account:

```bash
sudo useradd -m ssh-test
sudo passwd ssh-test
```

Try:

```bash
ssh ssh-test@192.168.0.xxx
```

After the experiment:

```bash
sudo userdel -r ssh-test
```

## Lesson

A working test user is a control case.

It helps separate:

```text
server-wide problem
```

from:

```text
one-account problem
```

---

# Part 9: Check `/etc/nologin`

At the physical console:

```bash
ls -l /etc/nologin
```

If the file exists:

```bash
cat /etc/nologin
```

## Questions

1. Does `/etc/nologin` exist?
    
2. What message does it contain?
    
3. Does root behave differently from normal users?
    
4. Was the file intentionally created?
    

Do not remove it until you understand why it exists.

---

# Part 10: Test Interactive Shell vs Non-Interactive Command

From the development machine, try an ordinary interactive login:

```bash
ssh deploy@192.168.0.xxx
```

Then try a non-interactive command:

```bash
ssh deploy@192.168.0.xxx 'id; pwd; echo SSH-COMMAND-WORKED'
```

Then force TTY allocation:

```bash
ssh -tt deploy@192.168.0.xxx
```

Then disable TTY allocation:

```bash
ssh -T deploy@192.168.0.xxx 'id; echo NO-TTY-WORKED'
```

## Interpretation

### Remote command works, but interactive login closes

Focus on:

```text
shell startup files
TTY allocation
interactive-shell configuration
PermitTTY
```

### Both remote command and interactive login close

Focus on:

```text
account policy
ForceCommand
authorized_keys options
shell path
/etc/nologin
Match blocks
PAM
```

### Password or key is rejected before either test runs

Focus on:

```text
authentication
authorized_keys
PasswordAuthentication
PubkeyAuthentication
account lock
```

---

# Part 11: Inspect Shell Startup Files

If the problem affects one user and authentication succeeds, inspect that user’s startup files:

```bash
sudo ls -la /home/deploy
sudo sed -n '1,240p' /home/deploy/.bash_profile
sudo sed -n '1,240p' /home/deploy/.bashrc
sudo sed -n '1,240p' /home/deploy/.profile
```

Some files may not exist. That is normal.

Also inspect SSH-specific startup files:

```bash
sudo ls -l /home/deploy/.ssh/rc
sudo ls -l /etc/ssh/sshrc
```

Look for commands such as:

```bash
exit
logout
exec ...
return
false
```

Also look for a command that fails and terminates the shell because of:

```bash
set -e
```

## Controlled experiment for Bash users

Back up the files first:

```bash
sudo cp -a /home/deploy/.bashrc /home/deploy/.bashrc.bak
sudo cp -a /home/deploy/.bash_profile /home/deploy/.bash_profile.bak
```

Only run a backup command if the source file exists.

Try bypassing normal Bash startup files:

```bash
ssh -tt deploy@192.168.0.xxx '/bin/bash --noprofile --norc'
```

## Questions

1. Does the clean Bash shell remain open?
    
2. If so, which startup file is likely involved?
    
3. What line causes the shell to exit?
    
4. Why is commenting out the entire file a poor permanent fix?
    

## Lesson

The goal is to identify the failing line, not erase the evidence.

---

# Part 12: Inspect User-Specific SSH Key Restrictions

If public-key authentication succeeds but the session closes, inspect:

```bash
sudo nl -ba /home/deploy/.ssh/authorized_keys
```

Look at the beginning of each key line.

A key may include options such as:

```text
command="..."
restrict
no-pty
```

Also inspect ownership and permissions:

```bash
sudo ls -ld /home/deploy
sudo ls -ld /home/deploy/.ssh
sudo ls -l /home/deploy/.ssh/authorized_keys
```

Expected general permissions:

```text
~/.ssh                 700
~/.ssh/authorized_keys 600
```

## Questions

1. Is the key restricted to a forced command?
    
2. Is TTY allocation disabled for that key?
    
3. Is the key intended only for file transfer or automation?
    
4. Does the restriction explain why an interactive shell closes?
    

---

# Part 13: Check `Match` Blocks and Effective User-Specific Configuration

An SSH configuration may behave differently for different users or client IP addresses.

Inspect the configuration:

```bash
sudo grep -RniE \
  '^(Match|AllowUsers|DenyUsers|AllowGroups|DenyGroups|ForceCommand|PermitTTY|ChrootDirectory|PasswordAuthentication|PubkeyAuthentication)' \
  /etc/ssh/sshd_config /etc/ssh/sshd_config.d
```

Find the client IP address on the development machine:

```bash
hostname -I
```

At the physical console, display effective SSH configuration for the failing user and client address:

```bash
sudo sshd -T \
  -C user=deploy,addr=192.168.0.CLIENT_IP,host=client-hostname \
  | grep -Ei \
  'port|listenaddress|allowusers|denyusers|allowgroups|denygroups|forcecommand|permittty|chrootdirectory|maxsessions|passwordauthentication|pubkeyauthentication'
```

Replace:

```text
192.168.0.CLIENT_IP
client-hostname
```

with actual values.

## Questions

1. Is a `Match User deploy` block active?
    
2. Is a source-IP-specific rule active?
    
3. Does a `ForceCommand` run instead of a normal shell?
    
4. Is the user chrooted?
    
5. Is `PermitTTY` disabled?
    
6. Are login sessions prohibited while forwarding remains allowed?
    

---

# Part 14: Inspect SELinux Only If Evidence Points There

Check SELinux mode:

```bash
getenforce
```

Inspect recent access-control denials:

```bash
sudo ausearch -m AVC -ts recent
```

Also try:

```bash
sudo journalctl --since "15 minutes ago" | grep -i avc
```

## Questions

1. Are there SELinux denials corresponding to the SSH login attempt?
    
2. What process was denied?
    
3. What resource was denied?
    
4. Is SELinux actually involved, or are you merely suspicious because AlmaLinux uses it?
    

## Rule

Do not run:

```bash
sudo setenforce 0
```

as a reflex.

That suppresses a security layer instead of teaching you what failed.

---

# Part 15: Reload Safely After a Configuration Fix

Before changing SSH configuration:

```bash
sudo cp -a /etc/ssh/sshd_config \
  /etc/ssh/sshd_config.bak.$(date +%Y%m%d-%H%M%S)
```

If using drop-in files:

```bash
sudo cp -a /etc/ssh/sshd_config.d \
  /etc/ssh/sshd_config.d.bak.$(date +%Y%m%d-%H%M%S)
```

After making the smallest necessary edit:

```bash
sudo sshd -t
```

Only if that succeeds:

```bash
sudo systemctl reload sshd
```

Check:

```bash
systemctl status sshd
sudo journalctl -u sshd --since "5 minutes ago"
```

From the development machine, open a new terminal and test:

```bash
ssh -vvv deploy@192.168.0.xxx
```

Keep the physical-console login open until remote SSH succeeds.

---

# Part 16: Complete the Diagnosis Report

Write:

```text
Symptom:

Exact client command:

Last meaningful lines from ssh -vvv:

Did TCP connection succeed?

Did SSH handshake succeed?

Did authentication succeed?

Did the session close before or after authentication?

Relevant server log message:

Did the problem affect one user or all users?

Was sshd running?

Was sshd listening on port 22?

Was SSH allowed through firewalld?

Was /etc/nologin present?

Configured login shell:

Did local login work?

Did a non-interactive SSH command work?

Did forcing a TTY change the result?

Did bypassing Bash startup files change the result?

Was a Match block active?

Were authorized_keys restrictions active?

Were SELinux AVC denials present?

Root cause:

Smallest correct fix:

How the fix was verified:

How the change could be undone:
```

---

# Troubleshooting Decision Tree

Use this compact sequence:

```text
1. ssh -vvv from client
2. journalctl -fu sshd at server console
3. classify: timeout, refused, auth failure, or post-auth close
4. verify IP address
5. verify sshd status
6. verify listening port
7. verify firewall zone and ssh service
8. compare two user accounts
9. test local login with sudo -iu USER
10. inspect account shell and expiry
11. inspect /etc/nologin
12. compare interactive and non-interactive SSH commands
13. inspect shell startup files
14. inspect authorized_keys restrictions
15. inspect Match blocks with sshd -T -C
16. inspect SELinux only if logs show evidence
17. apply smallest fix
18. validate with sshd -t
19. reload sshd
20. verify with a new remote session
```

---

# Completion Checkpoint

Explain:

1. Why is `Connection closed` different from `Connection refused`?
    
2. What does `ssh -vvv` reveal?
    
3. Why should server logs be watched while reproducing the problem?
    
4. What is the difference between `sshd` running and port 22 listening?
    
5. What is the difference between a listening port and a firewall-allowed port?
    
6. Why test multiple user accounts?
    
7. Why test local login?
    
8. Why compare interactive and non-interactive SSH sessions?
    
9. What does the login shell field in `/etc/passwd` control?
    
10. What does `/etc/nologin` do?
    
11. Why can a shell startup file close an SSH session?
    
12. What can a `Match` block change?
    
13. What does `ForceCommand` do?
    
14. What does `PermitTTY` control?
    
15. Why should SELinux not be disabled casually?
    
16. Why must `sshd -t` run before reloading SSH configuration?
    
17. Why should the physical-console session remain open during repair?
    

## What he should pay the most attention to

Because the message says:

```text
Connection to 192.168.0.xxx closed.
```

rather than:

```text
Connection refused
```

he should begin with:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.xxx
```

and, simultaneously at the physical console:

```bash
sudo journalctl -fu sshd
```

That pair is the core exercise. Everything else follows from the evidence.

OpenSSH documents that `sshd` handles the handshake, authentication, session preparation, and finally execution of the user’s shell or command. It also documents that `/etc/nologin`, user startup behavior, and the configured login shell can affect what happens after successful authentication. ([man.openbsd.org](https://man.openbsd.org/sshd.8 "sshd(8) - OpenBSD manual pages")) Red Hat documents that `/var/log/secure` stores security and authentication-related messages and that `journalctl` can filter systemd-service logs. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/assembly_troubleshooting-problems-using-log-files_configuring-basic-system-settings "Chapter 6. Troubleshooting problems by using log files | Configuring basic system settings | Red Hat Enterprise Linux | 9 | Red Hat Documentation"))

The central lesson is:

```text
Observe → classify → isolate → change one thing → verify
```

not:

```text
Search error message → paste commands → hope
```


[reference](https://chatgpt.com/s/t_6a2956a522488191a4a6c745e07c4aef)
