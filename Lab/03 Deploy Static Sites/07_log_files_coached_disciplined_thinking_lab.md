#LAB #deployment
#ssh
# 07 — Log Files: Coached Lab for Disciplined Troubleshooting
---

## What is a log?

Imagine the server is a store.

A customer says:

```text
I came to the store, but I could not buy anything.
```

That complaint is useful, but too vague.

A log is the store’s notebook:

```text
10:03 — customer entered
10:04 — asked for item A
10:04 — item A was missing
10:05 — customer left
```

Now you know the customer did enter the store. The problem was not the door. The problem was the missing item.

In Linux:

```text
symptom = what the user saw
log = what the machine recorded
```

---

# Rules for this lab

Before each important command, write:

```text
I am testing:
If I am right, I expect:
If I am wrong, I expect:
```

After the command, write:

```text
Exact evidence:
What this proves:
What this does NOT prove:
Next narrow question:
```

Do not write:

```text
worked
didn't work
probably
obviously
I don't know
```

---

# Part 1 — Find the important logs

On `app01`:

```bash
cd /var/log
ls -lah
```

Look for:

```text
messages
secure
nginx/
```

Now run:

```bash
sudo less /var/log/secure
```

Inside `less`, practice:

```text
/sshd
n
N
G
g
q
```

---

# Part 2 — Watch SSH logs live

Open one terminal on `app01`:

```bash
sudo tail -f /var/log/secure
```

From another machine, attempt a successful SSH login:

```bash
ssh deploy@app01
```

Then attempt a failed login by using the wrong password.

Stop `tail -f` with:

```text
Ctrl-C
```

## Write the evidence

```text
Successful login log line:

Failed login log line:

Difference between the two:
```

## Pay careful attention

Do not just write:

```text
one worked, one failed
```

Quote the exact log words.

Look for words like:

```text
Accepted
Failed
password
publickey
session opened
session closed
```

## Green check

```text
[ ] I reproduced an event.
[ ] I watched the log while reproducing it.
[ ] I quoted the successful login line.
[ ] I quoted the failed login line.
```

---

# Part 3 — Use `journalctl` for one service

Run:

```bash
sudo journalctl -u sshd --since "15 minutes ago"
```

Then:

```bash
sudo journalctl -u sshd -f
```

From another machine, attempt another SSH login.

Stop with:

```text
Ctrl-C
```

## Feynman explanation

Write:

```text
journalctl is like:
-u sshd means:
--since "15 minutes ago" helps because:
-f helps because:
```

Expected idea:

```text
journalctl reads systemd's journal.
-u sshd shows logs only for the SSH service.
--since limits the time window.
-f follows new log lines as they happen.
```

## Green check

```text
[ ] I filtered logs to one service.
[ ] I filtered logs by time.
[ ] I followed logs live.
[ ] I can explain the difference between `/var/log/secure` and `journalctl -u sshd`.
```

---

# Part 4 — Filter instead of drowning

Run:

```bash
sudo grep sshd /var/log/secure | tail -n 10
```

Then:

```bash
sudo grep -Ei 'accepted|failed|session opened|session closed' /var/log/secure | tail -n 20
```

## Slow down here

This is not magic. Break the pipeline into pieces.

First:

```bash
sudo grep sshd /var/log/secure
```

Then:

```bash
sudo grep sshd /var/log/secure | tail -n 10
```

## Write

```text
grep sshd does:

tail -n 10 does:

The pipe `|` does:
```

Expected idea:

```text
grep filters lines.
tail keeps the last lines.
The pipe sends output from the first command into the second command.
```

## Green check

```text
[ ] I ran the command in pieces first.
[ ] I understand what each piece does.
[ ] I did not copy a pipeline blindly.
```

---

# Part 5 — Nginx access log: prove the request reached the server

On `app01`, find Nginx logs:

```bash
sudo ls -lah /var/log/nginx
```

Watch the access log:

```bash
sudo tail -f /var/log/nginx/site1.access.log
```

If that file does not exist, check:

```bash
sudo tail -f /var/log/nginx/access.log
```

From another machine:

```bash
curl http://app01/
curl http://app01/not-here
```

Stop `tail -f`.

## Write exact evidence

```text
Log line for `/`:

Log line for `/not-here`:

Status code for `/`:

Status code for `/not-here`:
```

## Pay attention to the status code

Common codes:

```text
200 = success
403 = forbidden
404 = not found
500 = server error
```

## Feynman explanation

Write:

```text
A 404 means the request reached Nginx, but:
A 403 means the request reached Nginx, but:
A timeout means:
```

Expected idea:

```text
404: Nginx was reached, but the requested file/path was not found.
403: Nginx was reached, but access was forbidden.
Timeout: the client may not have reached the service at all.
```

## Green check

```text
[ ] I made a request.
[ ] I found the matching access-log line.
[ ] I identified the status code.
[ ] I did not confuse 404 with firewall failure.
```

---

# Part 6 — Nginx error log: prove the failure reason

Break file permission on purpose:

```bash
ssh deploy@app01
chmod 000 /var/www/site1/public/index.html
exit
```

From another machine:

```bash
curl -v http://app01/
```

Now inspect the error log:

```bash
sudo tail -n 30 /var/log/nginx/site1.error.log
```

If that file does not exist:

```bash
sudo tail -n 30 /var/log/nginx/error.log
```

Restore permission:

```bash
ssh deploy@app01
chmod 644 /var/www/site1/public/index.html
exit
```

Test again:

```bash
curl -v http://app01/
```

## Write

```text
Browser/curl symptom:

Exact error-log line:

The real problem was:

How I verified the fix:
```

## Slow down here

The browser may only say:

```text
403 Forbidden
```

The error log may say something more useful, such as:

```text
permission denied
```

The browser gives the symptom. The log gives the clue.

## Green check

```text
[ ] I created a controlled failure.
[ ] I read the error log.
[ ] I restored the system.
[ ] I verified after restoring.
```

---

# Part 7 — Compare `systemctl status` and logs

Run:

```bash
systemctl status nginx
```

Then:

```bash
sudo journalctl -u nginx --since "15 minutes ago"
```

Write:

```text
systemctl status is best for:

journalctl -u nginx is best for:
```

Expected idea:

```text
systemctl status shows current service state and a few recent lines.
journalctl shows more historical service logs.
```

## Green check

```text
[ ] I know when to use `systemctl status`.
[ ] I know when to use `journalctl`.
```

---

# Part 8 — Mini troubleshooting challenge

Scenario:

```text
The browser cannot display http://app01/
```

Do not fix yet. Investigate.

Use this order:

```bash
systemctl status nginx
sudo ss -tulpn | grep ':80'
sudo firewall-cmd --list-all
curl -v http://localhost/
curl -v http://app01/
sudo journalctl -u nginx --since "15 minutes ago"
sudo tail -n 30 /var/log/nginx/site1.access.log
sudo tail -n 30 /var/log/nginx/site1.error.log
ls -l /var/www/site1/public
namei -l /var/www/site1/public/index.html
```

For each command, write one line only:

```text
Command:
Question it answers:
Evidence:
Conclusion:
```

## Do not rush

The goal is not to run all commands quickly.

The goal is to stop as soon as evidence points to the layer.

Example:

```text
If access.log shows the request, the firewall is probably not the main issue.
If error.log shows permission denied, inspect file and directory permissions.
If ss shows no port 80 listener, inspect Nginx service state.
```

---

# Part 9 — One-page reflection

After the lab, write this:

```text
Most useful log command:

One log line I understood clearly:

One mistake I almost made:

One assumption the logs disproved:

One thing I fixed or restored:

The next time a service fails, my first three steps will be:
1.
2.
3.
```

Keep it short. The point is reflection, not an essay.

---

# Completion checklist

Do not move on until these are true:

```text
[ ] I can inspect `/var/log/secure`.
[ ] I can follow SSH logs live.
[ ] I can use `journalctl -u SERVICE --since TIME`.
[ ] I can follow service logs live with `journalctl -f -u SERVICE`.
[ ] I can explain a pipe using `grep ... | tail ...`.
[ ] I can find an Nginx request in the access log.
[ ] I can find an Nginx problem in the error log.
[ ] I can explain 200, 403, and 404 in plain English.
[ ] I can say what a command proves and what it does not prove.
```

---

# Final standard

Do not say:

```text
I checked the logs.
```

Say:

```text
The access log shows `GET /not-here HTTP/1.1` with status `404`, so the request reached Nginx. The next question is why that path does not exist.
```

That is disciplined thinking.
