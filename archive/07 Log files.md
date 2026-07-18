#log 
Yes. This should come **before** he proceeds far into Nginx deployment.

Do not give him a separate 500-page logging book yet. He needs a focused logging module tied to his actual machines:

```text
Ubuntu client / dev machine
        ↓ SSH
AlmaLinux app01
        ↓
systemd services, SSH attempts, firewall activity, Nginx requests and errors
```

Correction: more natural wording is: **“He has not learned how to inspect log files yet.”**

# What he should understand first

A log is not merely an error dump. It is a record of events.

Logs help answer:

- Did the request reach the machine?
    
- Did SSH accept or reject the login?
    
- Did Nginx start?
    
- Did Nginx receive the browser request?
    
- Did Nginx find the requested file?
    
- Did Linux deny access because of permissions?
    
- Did a scheduled job run?
    
- What changed shortly before the failure?
    

For an infrastructure professional, the basic workflow is:

```text
observe symptom
→ inspect relevant log
→ narrow the time range
→ filter noise
→ form hypothesis
→ change one thing
→ reproduce problem
→ inspect log again
```

That is more important than memorizing commands.

# Reading order

## 1. Read Ward Ch. 7.1 now

In **_How Linux Works_**, Ward Ch. 7 begins with exactly the right material:

- **7.1 System Logging**
    
- **7.1.1 Checking Your Log Setup**
    
- **7.1.2 Searching and Monitoring Logs**
    
- **7.1.3 Logfile Rotation**
    
- **7.1.4 Journal Maintenance**
    
- **7.1.5 A Closer Look at System Logging** ([オライリー](https://www.oreilly.com/library/view/how-linux-works/9781098128913/ "How Linux Works, 3rd Edition [Book]"))
    

For his current stage:

|Section|Priority|What to do|
|---|--:|---|
|7.1|High|Read carefully.|
|7.1.1|High|Inspect his Ubuntu and AlmaLinux machines.|
|7.1.2|Very high|Practice searching and following logs live.|
|7.1.3|Medium|Understand why logs cannot grow forever. Do not configure much yet.|
|7.1.4|Medium|Understand journal storage and cleanup.|
|7.1.5|Skim first|Return later after practical experience.|

Do not make him finish all of Ward Ch. 7 before touching logs. Read 7.1–7.1.2, then run experiments immediately.

---

## 2. Read selected LCL chapters alongside Ward

From **_The Linux Command Line_**, use these sections:

|LCL chapter|Relevant skill|Priority|
|---|---|--:|
|**Ch. 3 — Exploring the System**|Viewing file contents with `less`|Read now|
|**Ch. 6 — Redirection**|stdout, stderr, pipes|Read now|
|**Ch. 19 — Regular Expressions**|Filtering logs with `grep` patterns|Read soon|
|**Ch. 20 — Text Processing**|`cat`, `sort`, `uniq`, `cut`, and related tools|Read selectively later|

Author/Shotts Ch. 3 explicitly includes viewing files with `less`; Ch. 6 covers standard input, output, error, redirection, and pipelines; Ch. 19 covers regular expressions and `grep`; Ch. 20 introduces text-processing tools such as `cat`, `sort`, `uniq`, and `cut`. ([オライリー](https://www.oreilly.com/library/view/the-linux-command/9781492071235/ "The Linux Command Line, 2nd Edition [Book]"))

For now, he does **not** need to master `awk`, `sed`, or complex regular expressions. Start with:

```bash
less
tail
tail -f
grep
grep -i
grep -n
grep -E
journalctl
```

# Best articles and documentation

Use documentation in this order.

## First: Red Hat’s log troubleshooting chapter

Read:

**Red Hat Enterprise Linux: “Troubleshooting problems by using log files”**

This is directly relevant to AlmaLinux because AlmaLinux follows the RHEL ecosystem closely.

It explains common log locations such as:

```text
/var/log/messages
/var/log/secure
/var/log/cron
/var/log/boot.log
```

and introduces `journalctl`, boot filtering, service filtering, and boot history. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

Focus on:

- **2.2 System log files**
    
- **2.3 Commands for viewing logs**
    
- **2.3.1 Viewing system information**
    
- **2.3.2 Viewing information about specific services**
    
- **2.3.3 Viewing logs related to specific boots**
    

Do not read remote log aggregation yet.

---

## Second: official `journalctl` manual page

Read only enough to learn how to search the manual:

```bash
man journalctl
```

The official systemd documentation states that `journalctl` prints log entries stored by `systemd-journald`. `systemd-journald` collects and stores structured journal data from sources including kernel messages and system services. ([freedesktop](https://www.freedesktop.org/software/systemd/man/journalctl.html?utm_source=chatgpt.com "journalctl"))

He should search inside the man page for:

```text
/-u
/-f
/--since
/--boot
/-p
/-n
```

Do not read the whole man page linearly.

---

## Third: Nginx logging documentation

After Nginx is installed, read:

**NGINX documentation: Configuring Logging**

Nginx has two logs he needs immediately:

|Log|Purpose|
|---|---|
|Access log|Records processed client requests|
|Error log|Records problems with different severity levels|

Nginx’s documentation explains that the `access_log` directive controls request logging and the `error_log` directive controls issue logging and severity. ([nginx.org](https://nginx.org/en/docs/http/ngx_http_log_module.html?utm_source=chatgpt.com "Module ngx_http_log_module"))

Also use the DigitalOcean Nginx log guide as a beginner-friendly walkthrough. It explains access logs, error logs, severity levels, separate logs per virtual host, real-time monitoring with `tail -f`, and basic analysis with tools such as `grep`, `cut`, and `awk`. ([DigitalOcean](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs "NGINX Logs Explained: Access and Error Log Guide | DigitalOcean"))

He does not need custom JSON logs or centralized observability yet.

# Recommended video tutorial

Watch this first:

**Learn Linux TV — “journalctl Basics: How to Easily Check Your Linux Logs”**

It covers:

- basic `journalctl`
    
- inspecting one service
    
- following logs live
    
- troubleshooting OpenSSH
    
- filtering by time
    
- viewing entries from a user
    
- journal size cleanup ([YouTube](https://www.youtube.com/watch?v=0dG3vUYt7Uk&vl=en-US&utm_source=chatgpt.com "journalctl Basics: How to Easily Check Your Linux Logs"))
    

This is appropriate for him because it is practical and short enough to follow with a terminal open.

Watch it actively:

```text
pause video
→ type command
→ inspect actual output on app01
→ explain what changed in the output
```

Do not let him watch three logging videos in a row. One video plus experiments is enough.

# Two-day learning module

## Day 1: Learn how to read logs

### Exercise 1: Explore `/var/log`

On `app01`:

```bash
cd /var/log
pwd
ls -lah
```

Then:

```bash
sudo less /var/log/messages
sudo less /var/log/secure
```

Inside `less`:

```text
/sshd
n
N
G
g
q
```

He should learn:

|Key|Meaning|
|---|---|
|`/pattern`|Search forward|
|`n`|Next match|
|`N`|Previous match|
|`G`|End of file|
|`g`|Beginning of file|
|`q`|Quit|

Ask him:

> Why use `less` rather than `cat` for a large log?

Expected answer:

> `less` lets you scroll, search, and inspect a large file without dumping everything onto the terminal.

---

### Exercise 2: Read recent lines

```bash
sudo tail -n 20 /var/log/secure
```

Then:

```bash
sudo tail -f /var/log/secure
```

From a second terminal, attempt an SSH login with an incorrect password.

Observe new log lines.

Stop following the log with:

```text
Ctrl-C
```

Lesson:

> `tail -f` lets you watch events appear while reproducing a problem.

---

### Exercise 3: Filter logs

```bash
sudo grep sshd /var/log/secure
sudo grep -i failed /var/log/secure
sudo grep -i accepted /var/log/secure
sudo grep -n sshd /var/log/secure
```

Then combine tools:

```bash
sudo grep sshd /var/log/secure | tail -n 10
```

Ask him to explain the pipe:

```text
grep output → becomes tail input
```

This connects directly to LCL Ch. 6.

---

## Day 2: Learn `journalctl`

### Exercise 4: Inspect the journal

On AlmaLinux:

```bash
journalctl
```

That output is too large. Good. He should feel the need to filter.

Now:

```bash
journalctl -b
journalctl -n 20
journalctl --since "10 minutes ago"
```

Red Hat documents `journalctl` for all collected entries and `journalctl -b` for entries associated with a boot. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

He should explain:

|Command|Meaning|
|---|---|
|`journalctl`|Show journal entries available to current user|
|`journalctl -b`|Show logs for current boot|
|`journalctl -n 20`|Show recent entries|
|`journalctl --since "10 minutes ago"`|Restrict time range|

---

### Exercise 5: Inspect one systemd service

Use SSH first:

```bash
sudo journalctl -u sshd
sudo journalctl -u sshd --since "10 minutes ago"
sudo journalctl -u sshd -f
```

From another machine, attempt:

1. successful login
    
2. failed login
    

Observe differences.

Then inspect firewalld:

```bash
sudo journalctl -u firewalld --since today
```

Later, inspect Nginx:

```bash
sudo journalctl -u nginx --since "10 minutes ago"
```

The key lesson:

> Start with the relevant service. Do not search every log on the machine unless necessary.

---

### Exercise 6: Compare `systemctl status` and `journalctl`

Run:

```bash
systemctl status sshd
sudo journalctl -u sshd --since "30 minutes ago"
```

Ask:

- Which command shows whether the service is currently active?
    
- Which command gives more historical detail?
    

Expected answer:

|Command|Best use|
|---|---|
|`systemctl status sshd`|Current service state plus a few recent messages|
|`journalctl -u sshd`|Deeper historical log inspection|

`systemctl` is used to inspect and control systemd service state, whereas `journalctl` reads journal entries. ([freedesktop](https://www.freedesktop.org/software/systemd/man/systemctl.html?utm_source=chatgpt.com "systemctl"))

# Nginx logging lab

Do this after Nginx serves the first static page.

## Step 1: Inspect log files

On `app01`:

```bash
sudo ls -lah /var/log/nginx
```

Likely files:

```text
access.log
error.log
```

Confirm exact locations from config:

```bash
sudo grep -R "access_log\|error_log" /etc/nginx
```

Do not blindly assume file paths. Nginx log destinations can be configured. ([DigitalOcean](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs "NGINX Logs Explained: Access and Error Log Guide | DigitalOcean"))

---

## Step 2: Follow access log live

On app01:

```bash
sudo tail -f /var/log/nginx/access.log
```

From another machine:

```bash
curl http://app01/
curl http://app01/index.html
curl http://app01/does-not-exist
```

Observe status codes:

```text
200
404
```

He should identify:

- client IP
    
- timestamp
    
- request method
    
- requested path
    
- HTTP status
    
- response size
    
- user agent, depending on configured format
    

Nginx access logs record processed requests and commonly include client address, request, status code, and response-related fields. ([DigitalOcean](https://www.digitalocean.com/community/tutorials/nginx-access-logs-error-logs "NGINX Logs Explained: Access and Error Log Guide | DigitalOcean"))

---

## Step 3: Trigger an Nginx permission failure

Temporarily break permissions:

```bash
sudo chmod 000 /var/www/site1/public/index.html
```

Request:

```bash
curl -v http://app01/
```

Inspect:

```bash
sudo tail -n 20 /var/log/nginx/error.log
```

Restore:

```bash
sudo chmod 644 /var/www/site1/public/index.html
```

Ask him:

> What did the browser tell you? What did the log tell you that the browser did not?

That is the lesson.

---

## Step 4: Trigger a config error safely

Edit the Nginx config and add a typo.

Before reloading:

```bash
sudo nginx -t
```

Observe the error.

Then inspect:

```bash
sudo journalctl -u nginx --since "5 minutes ago"
sudo tail -n 20 /var/log/nginx/error.log
```

Fix config. Test again:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Lesson:

> Validate configuration before applying it. Use logs when validation or startup fails.

# Weekly drills

These should take 5–10 minutes each.

## Drill A: SSH log

```bash
sudo journalctl -u sshd --since "15 minutes ago"
```

Question:

> How many failed login attempts occurred?

## Drill B: Nginx 404

```bash
curl http://app01/not-here
sudo tail -n 10 /var/log/nginx/access.log
```

Question:

> Which line proves the request reached Nginx?

## Drill C: Filter

```bash
sudo grep ' 404 ' /var/log/nginx/access.log
```

Question:

> Why include spaces around `404`?

## Drill D: Count statuses

```bash
sudo cut -d' ' -f9 /var/log/nginx/access.log | sort | uniq -c | sort -nr
```

Question:

> What does each stage of the pipeline do?

Do not require him to memorize this command. Require him to take it apart.

## Drill E: Recent service failure

```bash
sudo journalctl -u nginx --since "10 minutes ago"
```

Question:

> Why is a time filter useful?

## Drill F: Current boot

```bash
journalctl -b -p warning
```

Question:

> What does `-b` do? What does `-p warning` do?

# What not to introduce yet

Do not teach these yet:

- Elasticsearch
    
- Loki
    
- Splunk
    
- Graylog
    
- OpenTelemetry
    
- centralized log shipping
    
- SIEM platforms
    
- JSON log pipelines
    
- log dashboards
    
- alerting systems
    

Those matter later. Right now, they would hide the fundamentals.

He first needs to become comfortable with:

```text
/var/log
less
tail -f
grep
pipes
journalctl
systemctl status
Nginx access.log
Nginx error.log
```

# Best place in the larger sequence

Use this order:

1. Read Ward 7.1–7.1.2.
    
2. Review LCL Ch. 3 and Ch. 6.
    
3. Watch the Learn Linux TV `journalctl` video.
    
4. Run SSH logging experiments.
    
5. Install Nginx.
    
6. Run Nginx access/error log experiments.
    
7. Continue Ward Ch. 7.
    
8. Read Ward Ch. 8.
    
9. Return later to LCL Ch. 19 and selected parts of Ch. 20.
    

# Completion test

Before moving on, ask him to diagnose this:

```text
A browser cannot display http://app01.
```

He should not randomly paste fixes.

He should work through:

```bash
systemctl status nginx
ss -tulpn | grep ':80'
sudo firewall-cmd --list-all
curl -v http://localhost
curl -v http://app01
sudo journalctl -u nginx --since "10 minutes ago"
sudo tail -n 30 /var/log/nginx/access.log
sudo tail -n 30 /var/log/nginx/error.log
ls -l /var/www/site1/public
namei -l /var/www/site1/public/index.html
```

He should explain what each command proves or fails to prove.

That is the real skill: not “knowing logs,” but using logs to stop guessing.