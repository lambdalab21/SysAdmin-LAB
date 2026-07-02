# Log-Driven Troubleshooting Lab: SSH Connection Closes Immediately

## Scenario

Remote SSH login previously worked:

```bash
ssh deploy@app01
```

Now the client reports:

```text
Connection to 192.168.0.xxx closed.
```

You can still log in directly at the physical keyboard and monitor of `app01`.

Your goal is not merely to restore access.

Your goal is to practice this troubleshooting loop:

```text
observe
→ collect logs
→ form a hypothesis
→ design a test
→ test one assumption
→ draw a conclusion
→ make the smallest fix
→ verify
```

---

# Rules

1. Do not edit configuration immediately.
    
2. Do not disable the firewall.
    
3. Do not disable SELinux.
    
4. Do not use `chmod -R 777`.
    
5. Record the exact log message before attempting a fix.
    
6. Change only one variable at a time.
    
7. Keep the physical-console session open until a new remote SSH login succeeds.
    
8. After each test, state whether the evidence supports or weakens your hypothesis.
    

---

# Mental Model

An SSH login passes through several layers:

```text
SSH client
   ↓
correct IP address
   ↓
network path
   ↓
firewall
   ↓
TCP port 22
   ↓
sshd service
   ↓
SSH protocol handshake
   ↓
authentication
   ↓
account policy
   ↓
shell startup
```

A useful troubleshooter asks:

```text
How far did the request get?
```

The logs help answer that question.

---

# Part 1: Reproduce the Problem While Watching Logs

At the physical console of `app01`, open two terminals.

## Terminal A: Follow the SSH service journal

```bash
sudo journalctl -f -u sshd.service
```

## Terminal B: Follow authentication logs

```bash
sudo tail -f /var/log/secure
```

Now, from the development machine, attempt login:

```bash
ssh deploy@192.168.0.xxx
```

Record every new line that appears in Terminal A or Terminal B.

## Questions

1. Did any new log entry appear?
    
2. Did the log mention the client IP address?
    
3. Did the log mention the username?
    
4. Did authentication succeed or fail?
    
5. Did a session open?
    
6. Did the session immediately close?
    
7. What is the final meaningful log message?
    

Write:

```text
Client command:

Client-visible error:

New lines from journalctl:

New lines from /var/log/secure:

Initial hypothesis:
```

Do not continue until you have written an initial hypothesis.

---

# Part 2: Classify the Failure from the Logs

Use the log evidence to choose a branch.

## Branch A: No new SSH log entry appears

Possible interpretation:

```text
The request did not reach sshd.
```

Investigate:

- wrong IP address
    
- SSH daemon not listening
    
- firewall blocking traffic
    
- client connecting to a different machine
    
- network path problem
    

Go to Part 3.

---

## Branch B: Log shows failed authentication

Possible log patterns:

```text
Failed password for deploy
Invalid user deploy
Authentication refused
Failed publickey
```

Possible interpretation:

```text
The network path and sshd are working.
The failure is related to identity or authentication.
```

Investigate:

- wrong username
    
- wrong password
    
- missing public key
    
- wrong `authorized_keys` file
    
- incorrect `.ssh` ownership or permissions
    
- locked or expired account
    

Go to Part 4.

---

## Branch C: Log shows successful authentication followed by session close

Possible log patterns:

```text
Accepted publickey for deploy
Accepted password for deploy
pam_unix(sshd:session): session opened
pam_unix(sshd:session): session closed
```

Possible interpretation:

```text
The network path works.
The firewall works.
sshd works.
Authentication works.
The login session is exiting after authentication.
```

Investigate:

- login shell set to `/sbin/nologin` or `/bin/false`
    
- account expired
    
- `/etc/nologin`
    
- shell startup file running `exit`
    
- `ForceCommand`
    
- user-specific SSH restriction
    
- `Match User` configuration block
    

Go to Part 5.

---

## Branch D: Log shows sshd configuration or service failure

Possible patterns:

```text
Failed to start OpenSSH server daemon
Bad configuration option
Missing privilege separation directory
Could not load host key
```

Possible interpretation:

```text
The SSH service itself is unhealthy.
```

Go to Part 6.

---

# Part 3: If No SSH Log Appears, Test the Outer Layers

Use this part only if the SSH logs show no new entry during the failed attempt.

## Step 1: Verify the current server IP address

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

1. Does `app01` still have the expected IP address?
    
2. Did DHCP assign a different address?
    
3. Are you troubleshooting the correct machine?
    

---

## Step 2: Check whether sshd is running

At the physical console:

```bash
systemctl status sshd
```

Check whether it is enabled at boot:

```bash
systemctl is-enabled sshd
```

## Step 3: Check whether sshd is listening

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

## Step 4: Check firewall configuration

```bash
sudo firewall-cmd --state
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all
```

Look for:

```text
services: ... ssh ...
```

From the development machine:

```bash
nc -vz 192.168.0.xxx 22
```

## Reasoning questions

1. Is `sshd` active?
    
2. Is it listening on port 22?
    
3. Is SSH allowed through the active firewall zone?
    
4. Does `nc` reach port 22?
    
5. At which layer does the evidence stop?
    

Write:

```text
Hypothesis:

Evidence supporting it:

Evidence weakening it:

Conclusion:
```

---

# Part 4: If Authentication Failed, Investigate the Account

Use this part only if the logs show an authentication failure.

At the physical console:

```bash
getent passwd deploy
id deploy
sudo passwd -S deploy
sudo chage -l deploy
```

Inspect the SSH directory:

```bash
sudo ls -ld /home/deploy
sudo ls -ld /home/deploy/.ssh
sudo ls -l /home/deploy/.ssh/authorized_keys
```

Expected general permissions:

```text
/home/deploy                  owned by deploy
/home/deploy/.ssh             700
authorized_keys               600
```

Test a second account:

```bash
ssh your-admin-user@192.168.0.xxx
```

## Questions

1. Does the user exist?
    
2. Is the account locked?
    
3. Is the account expired?
    
4. Does the failure affect only `deploy`?
    
5. Does another account log in successfully?
    
6. Do the logs mention file ownership or permissions?
    
7. Is password login being attempted, or public-key login?
    

## Thinking process

If another user can log in:

```text
Do not investigate the firewall first.
The server-wide path already works.
Focus on the deploy account.
```

If all users fail authentication:

```text
Investigate global sshd authentication settings.
```

---

# Part 5: If Authentication Succeeded but the Session Closed

This is the most relevant branch if the client sees:

```text
Connection to 192.168.0.xxx closed.
```

and the logs show:

```text
Accepted ...
session opened
session closed
```

## Step 1: Inspect the configured login shell

At the physical console:

```bash
getent passwd deploy
```

Example:

```text
deploy:x:1002:1002::/home/deploy:/bin/bash
```

The final field is the login shell.

Inspect it directly:

```bash
getent passwd deploy | cut -d: -f7
```

Check whether it exists:

```bash
ls -l "$(getent passwd deploy | cut -d: -f7)"
```

Check valid shells:

```bash
cat /etc/shells
```

Look for shells such as:

```text
/sbin/nologin
/bin/false
```

## Questions

1. What shell is assigned to `deploy`?
    
2. Does it permit interactive login?
    
3. Does it exist?
    
4. Is it executable?
    

---

## Step 2: Test local login behavior

At the physical console:

```bash
sudo -iu deploy
```

If the shell exits immediately, that is important evidence.

## Questions

1. Does local login also close?
    
2. If local login fails, is the network the likely root cause?
    
3. What does this tell you about the shell or account configuration?
    

---

## Step 3: Compare an interactive session with a remote command

From the development machine:

```bash
ssh deploy@192.168.0.xxx
```

Then try:

```bash
ssh deploy@192.168.0.xxx 'id; pwd; echo REMOTE-COMMAND-WORKED'
```

## Interpretation

### Remote command works, but interactive login closes

Investigate:

```text
interactive shell startup files
TTY behavior
shell configuration
```

### Both commands close immediately

Investigate:

```text
login shell
account restrictions
ForceCommand
/etc/nologin
authorized_keys restrictions
```

---

## Step 4: Inspect shell startup files

At the physical console:

```bash
sudo ls -la /home/deploy
```

Inspect relevant files if they exist:

```bash
sudo sed -n '1,240p' /home/deploy/.bash_profile
sudo sed -n '1,240p' /home/deploy/.bashrc
sudo sed -n '1,240p' /home/deploy/.profile
```

Look for:

```bash
exit
logout
exec ...
false
set -e
```

Also inspect SSH-specific startup files:

```bash
sudo ls -l /home/deploy/.ssh/rc
sudo ls -l /etc/ssh/sshrc
```

## Controlled test

Try bypassing normal Bash startup files:

```bash
ssh -tt deploy@192.168.0.xxx '/bin/bash --noprofile --norc'
```

## Questions

1. Does a clean Bash shell stay open?
    
2. If yes, which startup file likely contains the problem?
    
3. Which exact line causes the session to exit?
    
4. What is the smallest fix?
    

---

## Step 5: Inspect `/etc/nologin`

```bash
ls -l /etc/nologin
```

If it exists:

```bash
cat /etc/nologin
```

## Questions

1. Does `/etc/nologin` exist?
    
2. Was it intentionally created?
    
3. Does the log mention it?
    
4. Why would ordinary users be prevented from logging in?
    

---

## Step 6: Inspect SSH-specific restrictions

Inspect the user’s authorized keys:

```bash
sudo nl -ba /home/deploy/.ssh/authorized_keys
```

Look for options at the beginning of a key line:

```text
command="..."
restrict
no-pty
```

Search SSH server configuration:

```bash
sudo grep -RniE \
  '^(Match|AllowUsers|DenyUsers|AllowGroups|DenyGroups|ForceCommand|PermitTTY|ChrootDirectory)' \
  /etc/ssh/sshd_config /etc/ssh/sshd_config.d
```

## Questions

1. Is there a `Match User deploy` rule?
    
2. Is a forced command configured?
    
3. Is TTY allocation disabled?
    
4. Is the key intended only for automation or file transfer?
    
5. Does the restriction explain the log evidence?
    

---

# Part 6: If sshd Is Unhealthy, Read Service Logs

At the physical console:

```bash
systemctl status sshd
```

Then:

```bash
sudo journalctl -xeu sshd.service
```

Validate configuration:

```bash
sudo sshd -t
```

No output generally means the syntax and key sanity checks passed.

Display the effective configuration:

```bash
sudo sshd -T | less
```

Search:

```text
/port
/listenaddress
/passwordauthentication
/pubkeyauthentication
/allowusers
/denyusers
/forcecommand
/permittty
/chrootdirectory
```

## Questions

1. What exact log line identifies the failure?
    
2. Is there a syntax error?
    
3. Is sshd listening on the expected port?
    
4. Does a `Match` block change behavior for one user?
    
5. Is the service inactive because of a configuration error?
    

## Safe change procedure

Before editing:

```bash
sudo cp -a /etc/ssh/sshd_config \
  /etc/ssh/sshd_config.bak.$(date +%Y%m%d-%H%M%S)
```

After the smallest necessary edit:

```bash
sudo sshd -t
```

Only if validation succeeds:

```bash
sudo systemctl reload sshd
```

Then:

```bash
systemctl status sshd
sudo journalctl -u sshd.service --since "5 minutes ago"
```

---

# Part 7: Use Client Debug Output to Confirm the Server-Log Conclusion

Only after examining server logs, run this from the development machine:

```bash
ssh -vvv -E app01-ssh-debug.log deploy@192.168.0.xxx
```

Inspect the end of the file:

```bash
tail -n 50 app01-ssh-debug.log
```

## Questions

1. Did TCP connection succeed?
    
2. Did SSH key exchange succeed?
    
3. Did authentication succeed?
    
4. Did the session close before or after authentication?
    
5. Does the client-side evidence agree with the server logs?
    

## Lesson

Client debugging does not replace server logs.

It provides another viewpoint.

---

# Part 8: Create the Diagnosis Report

Complete this before applying a permanent fix:

```text
Symptom:

Exact reproduction command:

Timestamp of test:

Relevant lines from journalctl:

Relevant lines from /var/log/secure:

Did the request reach sshd?

Did authentication succeed?

Did the session open?

Did the session close immediately?

Did another account work?

Did local login work?

Did a non-interactive remote command work?

Configured login shell:

Was /etc/nologin present?

Were startup files involved?

Were authorized_keys restrictions involved?

Were Match rules involved?

Root cause hypothesis:

Test designed to disprove the hypothesis:

Test result:

Conclusion:

Smallest corrective action:

How the correction was verified:

How the correction can be undone:
```

---

# Part 9: Verify the Fix

Keep the physical-console session open.

From the development machine, open a new terminal:

```bash
ssh deploy@192.168.0.xxx
```

Run:

```bash
whoami
hostname
pwd
```

Then exit:

```bash
exit
```

At the physical console, inspect logs:

```bash
sudo journalctl -u sshd.service --since "5 minutes ago"
```

```bash
sudo tail -n 50 /var/log/secure
```

## Questions

1. What log entries show successful authentication?
    
2. What log entries show session open and close?
    
3. Does the final behavior match the expected behavior?
    
4. Did the smallest fix solve the problem?
    

---

# Optional Controlled Break-and-Fix Exercises

Do these only after restoring normal SSH access.

Keep the physical-console session open during every exercise.

## Exercise A: Stop sshd

```bash
sudo systemctl stop sshd
```

From the development machine:

```bash
ssh deploy@app01
```

Observe:

- client error
    
- `journalctl`
    
- listening ports
    

Restore:

```bash
sudo systemctl start sshd
```

Lesson:

```text
A stopped service differs from an authenticated session that immediately closes.
```

---

## Exercise B: Remove SSH from the firewall temporarily

Inspect current state first:

```bash
sudo firewall-cmd --list-all
```

Remove SSH temporarily:

```bash
sudo firewall-cmd --remove-service=ssh
```

Test remotely:

```bash
ssh deploy@app01
```

Restore:

```bash
sudo firewall-cmd --add-service=ssh
```

Lesson:

```text
A firewall failure often prevents sshd from logging the attempted connection,
because the request never reaches sshd.
```

---

## Exercise C: Assign a non-login shell to a test account

Create a disposable account:

```bash
sudo useradd -m ssh-lab-user
sudo passwd ssh-lab-user
```

Assign a non-login shell:

```bash
sudo usermod -s /sbin/nologin ssh-lab-user
```

Attempt login:

```bash
ssh ssh-lab-user@app01
```

Watch:

```bash
sudo journalctl -f -u sshd.service
sudo tail -f /var/log/secure
```

Restore or delete:

```bash
sudo userdel -r ssh-lab-user
```

Lesson:

```text
Authentication can succeed while interactive login is still denied.
```

---

## Exercise D: Exit from `.bash_profile`

Create a disposable account:

```bash
sudo useradd -m shell-lab-user
sudo passwd shell-lab-user
```

Add:

```bash
echo 'exit' | sudo tee /home/shell-lab-user/.bash_profile
sudo chown shell-lab-user:shell-lab-user /home/shell-lab-user/.bash_profile
```

Attempt login:

```bash
ssh shell-lab-user@app01
```

Compare:

```bash
ssh shell-lab-user@app01 'id'
```

Inspect logs.

Delete the test account:

```bash
sudo userdel -r shell-lab-user
```

Lesson:

```text
A shell startup file can cause a post-authentication session close.
```

---

# Final Checkpoint

Explain:

1. Why is `Connection closed` not the same as `Connection refused`?
    
2. Why should logs be watched while reproducing the error?
    
3. Why is “no new log entry” useful evidence?
    
4. Why test another user?
    
5. Why test local login?
    
6. Why compare interactive login with a remote command?
    
7. Why inspect the login shell?
    
8. Why inspect `.bash_profile` and `.bashrc`?
    
9. What does `/etc/nologin` do?
    
10. Why should `sshd -t` run before reloading SSH configuration?
    
11. Why should the physical-console session stay open?
    
12. Why is changing one variable at a time important?
    
13. What is the difference between a hypothesis and a conclusion?
    
14. What evidence proved the root cause?