

Use **AlmaLinux `app01`** for most exercises. Compare with Ubuntu where noted.

---

# Safety rules

1. Do not change permissions on existing system logs.
2. Use `sudo` to read protected logs.
3. Do not delete logs.
4. Do not use `journalctl --vacuum-*` yet.
5. Do not force rotation of production-style logs.
6. For `logrotate`, use debug mode (`-d`) only.
7. Create synthetic events with `logger` so you know exactly what to search for.

---

# Part 1 — Orientation drills

## Drill 1: Explore `/var/log`

On `app01`:

```
cd /var/logpwdls -lah
```

Answer:

1. Which entries are regular files?
2. Which entries are directories?
3. Which filenames appear security-related?
4. Which filenames appear service-specific?
5. Which files require `sudo` to read?

Then run:

```
sudo find /var/log -maxdepth 2 -type f | sort | less
```

Inside `less`, practice:

```
/securitynNgGq
```

---

## Drill 2: Compare traditional logs on AlmaLinux and Ubuntu

On AlmaLinux:

```
sudo ls -l /var/log/messages /var/log/secure 2>/dev/null
```

On Ubuntu:

```
sudo ls -l /var/log/syslog /var/log/auth.log 2>/dev/null
```

Answer:

1. Which files exist on each distribution?
2. Which authentication log exists?
3. Why is memorizing only one filename a weak habit?
4. Which tool works across both systemd-based systems?

Expected tool:

```
journalctl
```

---

## Drill 3: Learn `less`

On AlmaLinux:

```
sudo less /var/log/secure
```

Practice:

```
/sshdnNGgq
```

Then answer:

1. Why is `less` better than `cat` for a large log?
2. How do you jump to the newest entries?
3. How do you search for `sshd`?
4. How do you move to the next match?

---

## Drill 4: Learn `tail`

```
sudo tail -n 20 /var/log/secure
```

Open a second terminal and run:

```
sudo tail -f /var/log/secure
```

From the development machine, attempt an SSH login to `app01`.

Stop following with:

```
Ctrl-C
```

Answer:

1. What new log lines appeared?
2. Could you identify the username?
3. Did you see a successful or failed login?
4. Why is `tail -f` useful while reproducing a problem?

---

## Drill 5: Learn `grep`

```
sudo grep sshd /var/log/secure | tail -n 10sudo grep -i failed /var/log/secure | tail -n 10sudo grep -i accepted /var/log/secure | tail -n 10sudo grep -n sshd /var/log/secure | tail -n 10
```

Answer:

1. What does `-i` do?
2. What does `-n` do?
3. What does the pipe do?
4. Why filter before using `tail`?

---

# Part 2 — Journal drills

## Drill 6: Inspect the journal without filters

```
journalctl
```

Do not stare at the noise for long. Quit with:

```
q
```

Now add filters:

```
journalctl -n 20journalctl -bjournalctl --since "10 minutes ago"
```

Answer:

1. Why is an unfiltered journal hard to use?
2. What does `-n 20` do?
3. What does `-b` do?
4. Why is `--since` useful?

---

## Drill 7: Inspect one service

On AlmaLinux:

```
sudo journalctl -u sshd --since "30 minutes ago"sudo journalctl -u sshd -n 20
```

On Ubuntu, the SSH unit may be named `ssh`:

```
sudo journalctl -u ssh --since "30 minutes ago"
```

Determine the actual unit name:

```
systemctl status sshdsystemctl status ssh
```

Answer:

1. Which unit name exists on each machine?
2. Why is service-specific filtering better than reading the entire journal?
3. Which command summarizes current service state?
4. Which command gives deeper history?

---

## Drill 8: Follow one service live

On `app01`:

```
sudo journalctl -u sshd -f
```

From another terminal:

1. Log in successfully.
2. Log out.
3. Attempt one login with a wrong password.

Observe the journal.

Answer:

1. Which entries correlate with the successful login?
2. Which entries correlate with the failed attempt?
3. Why should you intentionally create a known event during investigation?

---

## Drill 9: Filter by severity

```
journalctl -b -p warning
```

Then:

```
journalctl -b -p err
```

Answer:

1. Which output is narrower?
2. Why can severity filtering hide useful context?
3. When should you inspect surrounding informational messages too?

---

## Drill 10: Inspect journal storage

```
journalctl --disk-usage
```

Then inspect the manual:

```
man journalctl
```

Search inside the man page:

```
/--disk-usage/--vacuum-size
```

Do **not** run vacuum commands.

Answer:

1. Why do journals need storage controls?
2. Why should you inspect before cleaning?
3. Why can deleting logs harm troubleshooting?

---

# Part 3 — Generate known events with `logger`

## Drill 11: Write a synthetic event

```
logger -t ward71 "first synthetic event"
```

Find it:

```
journalctl -t ward71 --since "5 minutes ago"
```

Try another:

```
logger -t ward71 "scp deployment test started"logger -t ward71 "scp deployment test completed"journalctl -t ward71 --since "5 minutes ago"
```

Answer:

1. What does `-t ward71` do?
2. Why are synthetic events useful?
3. How could a shell script use `logger`?

---

## Drill 12: Pipe output into the journal

```
printf 'line one\nline two\n' | systemd-cat -t ward71-pipejournalctl -t ward71-pipe --since "5 minutes ago"
```

Answer:

1. What did the pipe do?
2. Why might a script send stdout or stderr into the journal?
3. What is the difference between a file log and the systemd journal?

---

# Part 4 — Log rotation inspection

## Drill 13: Inspect rotation configuration

```
ls -l /etc/logrotate.confls -l /etc/logrotate.dsudo less /etc/logrotate.conf
```

Inspect one service rule if available:

```
sudo grep -R "nginx\|messages\|secure" /etc/logrotate.conf /etc/logrotate.d 2>/dev/null
```

Answer:

1. Why rotate logs?
2. What happens if logs grow forever?
3. What does retention mean?
4. Why might rotated logs be compressed?

---

## Drill 14: Use debug mode only

```
sudo logrotate -d /etc/logrotate.conf
```

Answer:

1. Did debug mode modify logs?
2. Which logs were considered?
3. Which logs were skipped?
4. What conditions trigger rotation?

Do not use:

```
sudo logrotate -f /etc/logrotate.conf
```

yet.

---

# Part 5 — Investigation labs

## Lab 1: SSH authentication investigation

### Goal

Use logs to distinguish successful and failed SSH authentication.

### Procedure

On `app01`:

```
sudo journalctl -u sshd -f
```

From the development machine:

```
ssh deploy@app01
```

Log out, then attempt a login with the wrong password for a test account.

Afterward:

```
sudo journalctl -u sshd --since "10 minutes ago"sudo grep -i "failed\|accepted" /var/log/secure | tail -n 20
```

### Report

Write:

```
Symptom or event:Time window:Commands used:Relevant lines:What the evidence proves:What the evidence does not prove:
```

---

## Lab 2: Synthetic deployment audit trail

### Goal

Use `logger` in a simple deployment simulation.

### Procedure

On the development machine:

```
ssh deploy@app01 'logger -t site1-deploy "site1 deployment started"'scp -r ./dist/. deploy@app01:/var/www/site1/public/ssh deploy@app01 'logger -t site1-deploy "site1 deployment finished"'
```

On `app01`:

```
journalctl -t site1-deploy --since "10 minutes ago"
```

### Questions

1. Why log the start and end?
2. What happens if the `scp` command fails between them?
3. How would a future deployment script record failure explicitly?
4. Why is a deployment log useful when a site suddenly changes?

---

## Lab 3: Diagnose a file-copy permission failure

### Goal

Correlate a user-visible `scp` error with filesystem permissions.

### Procedure

On `app01`:

```
sudo mkdir -p /srv/log-lab/closedsudo chown root:root /srv/log-lab/closedsudo chmod 555 /srv/log-lab/closed
```

From the development machine:

```
scp hello.txt deploy@app01:/srv/log-lab/closed/
```

Then investigate on `app01`:

```
ls -ld /srv/log-lab/closednamei -l /srv/log-lab/closedtouch /srv/log-lab/closed/test.txt
```

### Questions

1. Did SSH login fail?
2. Did remote filesystem write permission fail?
3. Which evidence came from `scp`?
4. Which evidence came from `ls -ld`, `namei -l`, and `touch`?
5. Why might no useful application log exist for this failure?

### Lesson

Logs are important, but not every failure is best diagnosed through logs. Use the right evidence source.

---

## Lab 4: Nginx access-log investigation

Run this after Nginx is installed.

### Goal

Prove that a browser or `curl` request reached Nginx.

### Procedure

On `app01`:

```
sudo tail -f /var/log/nginx/access.log
```

From another machine:

```
curl -v http://app01/curl -v http://app01/index.htmlcurl -v http://app01/does-not-exist
```

### Questions

1. Which requests returned `200`?
2. Which request returned `404`?
3. Which line proves the request reached Nginx?
4. What information is visible in each access-log line?
5. If no new access-log line appears, what possible layers should you check next?

Possible layers:

```
DNS or /etc/hostsroutingfirewallport 80 listeningwrong hostclient-side issue
```

---

## Lab 5: Nginx permission-denied investigation

Run this after Nginx is installed.

### Goal

Use Nginx error logs to diagnose filesystem permission failure.

### Procedure

Back up permissions:

```
stat -c '%a %n' /var/www/site1/public/index.html
```

Temporarily break the file:

```
sudo chmod 000 /var/www/site1/public/index.html
```

Request the site:

```
curl -v http://app01/
```

Inspect evidence:

```
sudo tail -n 30 /var/log/nginx/error.logsudo journalctl -u nginx --since "5 minutes ago"namei -l /var/www/site1/public/index.html
```

Restore:

```
sudo chmod 644 /var/www/site1/public/index.html
```

### Questions

1. What did `curl` report?
2. What did the Nginx error log report?
3. What did `namei -l` reveal?
4. Why is the error log more useful than guessing?
5. Why should normal HTML files usually be `0644`, not `0755`?

---

## Lab 6: Nginx configuration-error investigation

Run this after Nginx is installed.

### Goal

Use validation and logs before restarting a service.

### Procedure

Back up the config:

```
sudo cp /etc/nginx/conf.d/site1.conf /etc/nginx/conf.d/site1.conf.bak
```

Introduce a harmless typo in the config.

Validate:

```
sudo nginx -t
```

Inspect:

```
sudo journalctl -u nginx --since "5 minutes ago"sudo tail -n 30 /var/log/nginx/error.log
```

Restore:

```
sudo cp /etc/nginx/conf.d/site1.conf.bak /etc/nginx/conf.d/site1.confsudo nginx -tsudo systemctl reload nginx
```

### Questions

1. Why run `nginx -t` before reload?
2. Did a reload occur while the config was invalid?
3. Which output gave the most direct explanation?
4. What is the difference between config validation and log investigation?

---

## Lab 7: Build a troubleshooting timeline

### Goal

Investigate events by time rather than randomly reading logs.

### Procedure

Create markers:

```
logger -t ward71-timeline "timeline lab started"sleep 3logger -t ward71-timeline "simulated deployment started"sleep 3logger -t ward71-timeline "simulated deployment failed: permission denied"sleep 3logger -t ward71-timeline "timeline lab finished"
```

Inspect:

```
journalctl -t ward71-timeline --since "10 minutes ago" --no-pager
```

Then inspect a narrow time range using actual timestamps from the output:

```
journalctl --since "YYYY-MM-DD HH:MM:SS" --until "YYYY-MM-DD HH:MM:SS"
```

### Questions

1. Why do timestamps matter?
2. Which events happened before the failure?
3. Which events happened after?
4. How does a narrow time window reduce noise?

---

# Part 6 — Five-minute recurring drills

Do one or two per study session.

## Drill A: Find recent SSH events

```
sudo journalctl -u sshd --since "15 minutes ago"
```

Question:

```
How many successful and failed login events can you identify?
```

## Drill B: Find your synthetic event

```
logger -t ward71 "practice marker"journalctl -t ward71 --since "2 minutes ago"
```

Question:

```
What timestamp was recorded?
```

## Drill C: Compare `status` and journal history

```
systemctl status sshdsudo journalctl -u sshd -n 20
```

Question:

```
Which command is better for current state? Which is better for history?
```

## Drill D: Search one text log

```
sudo grep -i sshd /var/log/secure | tail -n 10
```

Question:

```
What does each pipeline stage do?
```

## Drill E: Check journal size

```
journalctl --disk-usage
```

Question:

```
Why should logs not grow forever?
```

## Drill F: Inspect current boot warnings

```
journalctl -b -p warning
```

Question:

```
Which warning looks actionable? Which warnings may be harmless?
```

---

# Part 7 — Completion test

Scenario:

```
The browser cannot display http://app01.
```

He must not paste random fixes. Use this order:

```
systemctl status nginxss -tulpn | grep ':80'sudo firewall-cmd --list-allcurl -v http://localhostcurl -v http://app01sudo journalctl -u nginx --since "10 minutes ago"sudo tail -n 30 /var/log/nginx/access.logsudo tail -n 30 /var/log/nginx/error.logls -l /var/www/site1/publicnamei -l /var/www/site1/public/index.html
```

For each command, write:

```
What question does this answer?What evidence did I observe?What possibilities remain?
```

He passes only when he can explain why each command is in the sequence.



---


# Appendix

Created the Ward Section 7.1 training packet.

Ward Ch. 7.1 covers system logging, checking the logging setup, searching and monitoring logs, logfile rotation, journal maintenance, and the logging architecture. ([オライリー・メディア](https://www.oreilly.com/library/view/how-linux-works/9781098128913/c07.xhtml?utm_source=chatgpt.com "System Configuration - How Linux Works, 3rd Edition")) The exercises use `journalctl`, which prints entries stored in the systemd journal; `logger`, which creates synthetic log messages; and `logrotate -d`, which safely shows what rotation would do without modifying logs. ([man7.org](https://man7.org/linux/man-pages/man1/journalctl.1.html?utm_source=chatgpt.com "journalctl(1) - Linux manual page"))

## Files

- [Complete Markdown training packet](sandbox:/mnt/data/ward-7.1-log-investigation-training.md)
    
- [Quizlet TSV import file](sandbox:/mnt/data/ward-7.1-log-quizlet.tsv)
    
- [MySQL INSERT script](sandbox:/mnt/data/ward-7.1-log-quiz-mysql.sql)
    
- [Download all files as ZIP](sandbox:/mnt/data/ward-7.1-log-training-packet.zip)
    

The MySQL script uses:

```sql
SET @book_id = 1;
```

Change `1` to the correct `book_id` before importing.

## What is included

The packet contains:

- 14 short drills
    
- 7 investigation labs
    
- 6 recurring five-minute drills
    
- a final Nginx troubleshooting test
    
- 30 quiz questions with answers
    
- Quizlet TSV format
    
- MySQL `INSERT` statements
    

## Recommended order

Use the packet in this sequence:

1. Read Ward 7.1–7.1.2.
    
2. Complete Drills 1–12.
    
3. Run Labs 1–3 before Nginx deployment.
    
4. Install and configure Nginx.
    
5. Run Labs 4–6.
    
6. Complete the final troubleshooting test.
    
7. Read Ward 7.1.3–7.1.5 after he has seen real logs.
    

The exercises emphasize the correct habit:

```text
observe symptom
→ identify likely service
→ narrow the time window
→ inspect evidence
→ form one hypothesis
→ change one thing
→ reproduce
→ inspect evidence again
```

Red Hat’s documentation makes the same practical point: system logs contain messages from the kernel, services, and applications and are useful for troubleshooting and monitoring. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/assembly_troubleshooting-problems-using-log-files_configuring-basic-system-settings?utm_source=chatgpt.com "Chapter 7. Troubleshooting problems by using log files"))

