#Book/How-Linux-Works  #Author/Ward #Red-Hat #Linux #log 
Correction: _Create drills from Red Hat Documentation, “Chapter 2: Troubleshooting problems by using log files.”_

Below is a practical drill set for **AlmaLinux `app01`**, based on the Red Hat chapter. The chapter explains that Linux logs contain messages from the system, kernel, services, and applications; `systemd-journald` collects messages from the kernel, early boot, daemon output, and syslog; `rsyslog` sorts messages and writes persistent files under `/var/log`. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

# Drill set: Red Hat Ch. 2 log troubleshooting

## Setup rule

He should keep a lab notebook with this format:

```text
Command:
What question does this answer?
What evidence did I find?
What should I check next?
```

No guessing. Evidence first.

---

# Part 1 — Know the logging pieces

## Drill 1: Identify the logging services

On `app01`:

```bash
systemctl status systemd-journald
systemctl status rsyslog
```

Then:

```bash
systemctl is-enabled systemd-journald
systemctl is-enabled rsyslog
```

He should answer:

1. Is `systemd-journald` running?
    
2. Is `rsyslog` running?
    
3. Which one collects journal messages?
    
4. Which one writes many persistent files under `/var/log`?
    

Expected idea: `systemd-journald` collects messages from sources such as the kernel, early boot process, daemon stdout/stderr, and syslog; `rsyslog` sorts messages by type and priority and writes them to files under `/var/log`. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

---

## Drill 2: Inspect `/var/log`

```bash
cd /var/log
ls -lah
```

Now inspect these if they exist:

```bash
sudo ls -l /var/log/messages
sudo ls -l /var/log/secure
sudo ls -l /var/log/maillog
sudo ls -l /var/log/cron
sudo ls -l /var/log/boot.log
```

He should answer:

1. Which files exist?
    
2. Which ones need `sudo` to read?
    
3. Which one is likely for authentication?
    
4. Which one is likely for boot messages?
    
5. Which one is likely for scheduled jobs?
    

Red Hat lists `/var/log/messages`, `/var/log/secure`, `/var/log/maillog`, `/var/log/cron`, and `/var/log/boot.log` as important log files, with `/var/log/secure` containing security and authentication-related messages, `/var/log/cron` containing periodically executed task messages, and `/var/log/boot.log` containing startup messages. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

---

# Part 2 — Read logs without drowning

## Drill 3: Use `less` on `/var/log/secure`

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

He should answer:

1. What does `/sshd` do?
    
2. What does `n` do?
    
3. What does `G` do?
    
4. Why is `less` better than `cat` for logs?
    

Expected answer:

> `less` lets you search, scroll, and inspect large files without dumping the entire file into the terminal.

---

## Drill 4: Use `tail` for recent evidence

```bash
sudo tail -n 20 /var/log/secure
```

Then:

```bash
sudo tail -f /var/log/secure
```

From another terminal, attempt an SSH login to `app01`.

He should answer:

1. Did new lines appear?
    
2. Which username appears?
    
3. Which remote IP appears?
    
4. Was the login accepted or rejected?
    
5. Why is `tail -f` useful during troubleshooting?
    

---

## Drill 5: Use `grep` to reduce noise

```bash
sudo grep sshd /var/log/secure | tail -n 20
sudo grep -i failed /var/log/secure | tail -n 20
sudo grep -i accepted /var/log/secure | tail -n 20
```

He should explain this pipeline:

```bash
sudo grep sshd /var/log/secure | tail -n 20
```

Correct idea:

> First find lines containing `sshd`, then show only the last 20 matching lines.

---

# Part 3 — Learn basic `journalctl`

## Drill 6: Compare unfiltered and filtered journal output

Run:

```bash
journalctl
```

Quit with:

```text
q
```

Now run:

```bash
journalctl -n 20
journalctl -b
journalctl --since "10 minutes ago"
```

He should answer:

1. Why is plain `journalctl` noisy?
    
2. What does `-n 20` do?
    
3. What does `-b` do?
    
4. Why is `--since` useful?
    

Red Hat states that `journalctl` shows collected journal entries and `journalctl -b` shows logs for the current boot. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

---

## Drill 7: Search current boot logs

```bash
journalctl -b | grep sshd
journalctl -b | grep firewalld
journalctl -b | grep nginx
```

He should answer:

1. Which services produced messages during this boot?
    
2. Why is `journalctl -b` better than all boots for a current problem?
    
3. Why is `grep` useful but crude?
    

Expected idea:

> `grep` is quick text filtering, but service-specific journal filters are cleaner when the service is known.

---

# Part 4 — Investigate specific services

## Drill 8: Find the actual SSH service name

On AlmaLinux:

```bash
systemctl status sshd
```

Also try:

```bash
systemctl status ssh
```

He should answer:

1. Which service exists?
    
2. Which one fails?
    
3. Why should he check instead of assuming?
    

Now run:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service
```

Red Hat shows that `journalctl -b _SYSTEMD_UNIT=<name.service>` filters journal entries to a specific systemd service. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

---

## Drill 9: Follow SSH logs live

Terminal 1 on `app01`:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service -f
```

Terminal 2 from another machine:

```bash
ssh deploy@app01
```

Then try one failed login.

He should answer:

1. What appeared for the successful login?
    
2. What appeared for the failed login?
    
3. Why does watching logs live help?
    

---

## Drill 10: Compare service logs and text logs

Run:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service --since "15 minutes ago"
sudo grep -i sshd /var/log/secure | tail -n 20
```

He should answer:

1. Which output is easier to read?
    
2. Which output contains more context?
    
3. Which command would he use first for SSH authentication problems?
    
4. Why might both be useful?
    

---

# Part 5 — Learn PID filtering

## Drill 11: Find a service PID

```bash
systemctl status sshd
pgrep -a sshd
```

Pick one PID, then run:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service _PID=<PID>
```

Replace `<PID>` with the actual number.

Red Hat explains that service and PID filters can be combined, for example with `_SYSTEMD_UNIT=<name.service> _PID=<number>`. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

He should answer:

1. What did filtering by PID remove?
    
2. Why might PID filtering be useful?
    
3. Why can PID filtering miss messages from child processes or restarted services?
    

---

# Part 6 — Learn OR filters

## Drill 12: Compare two services

Run:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service _SYSTEMD_UNIT=firewalld.service
```

Then:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service + _SYSTEMD_UNIT=firewalld.service
```

He should answer:

1. Did both commands show both services?
    
2. What does Red Hat say about repeated matches for the same field?
    
3. What does `+` mean?
    

Red Hat says a plus sign combines expressions as logical OR, and repeated expressions for the same field can match either unit. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

Do not over-focus on this yet. It is useful, but not the main beginner skill.

---

# Part 7 — Investigate specific boots

## Drill 13: List boots

```bash
journalctl --list-boots
```

He should answer:

1. How many boots are listed?
    
2. Which one is current?
    
3. What is a boot ID?
    
4. What timestamps are shown?
    

Red Hat says `journalctl --list-boots` shows boot numbers, boot IDs, and first/last message timestamps for each boot. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

---

## Drill 14: Inspect logs from one boot

Copy a boot ID from:

```bash
journalctl --list-boots
```

Then run:

```bash
sudo journalctl --boot=<BOOT_ID> _SYSTEMD_UNIT=sshd.service
```

He should answer:

1. Which boot did he inspect?
    
2. Why is this useful after a reboot?
    
3. What problem would be hard to diagnose if he only looked at current logs?
    

Expected idea:

> If a failure happened before reboot, current-boot logs may not contain the evidence.

---

# Part 8 — File-path journal queries

## Drill 15: Query logs related to a device path

First list block devices:

```bash
lsblk
```

Then try a real device path, such as:

```bash
journalctl /dev/sda
```

or if the system uses NVMe:

```bash
journalctl /dev/nvme0n1
```

Red Hat says `journalctl FILEPATH` shows logs related to a specific file path, with `/dev/sda` as an example. ([docs.redhat.com](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/risk_reduction_and_recovery_operations/troubleshooting-problems-by-using-log-files "Chapter 2. Troubleshooting problems by using log files | Risk reduction and recovery operations | Red Hat Enterprise Linux | 10 | Red Hat Documentation"))

He should answer:

1. Did the command return anything?
    
2. What device path exists on this machine?
    
3. Why should he use `lsblk` before blindly copying `/dev/sda`?
    

This matters because not every machine has `/dev/sda`.

---

# Part 9 — Investigation scenarios

## Scenario 1: Failed SSH login

Create one failed SSH login attempt from another machine.

Then investigate:

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service --since "10 minutes ago"
sudo grep -i failed /var/log/secure | tail -n 20
```

Write:

```text
Symptom:
Time window:
Likely service:
Journal evidence:
Text-log evidence:
Conclusion:
```

He should conclude whether the failure was authentication-related, not network-related.

---

## Scenario 2: Was the firewall touched recently?

Run:

```bash
sudo journalctl -b _SYSTEMD_UNIT=firewalld.service --since "30 minutes ago"
sudo firewall-cmd --list-all
```

He should answer:

1. Did firewalld log anything recently?
    
2. What services and ports are currently allowed?
    
3. Why do logs and current configuration answer different questions?
    

Correct distinction:

```text
logs = what happened
current config = what is true now
```

---

## Scenario 3: Did Nginx start in this boot?

After Nginx is installed:

```bash
sudo journalctl -b _SYSTEMD_UNIT=nginx.service
systemctl status nginx
```

He should answer:

1. Did Nginx start successfully?
    
2. Did it log config errors?
    
3. Is it active now?
    
4. What does `journalctl` show that `systemctl status` does not?
    

---

## Scenario 4: The site does not load

Use this sequence:

```bash
systemctl status nginx
ss -tulpn | grep ':80'
sudo firewall-cmd --list-all
curl -v http://localhost
curl -v http://app01
sudo journalctl -b _SYSTEMD_UNIT=nginx.service --since "10 minutes ago"
sudo tail -n 30 /var/log/nginx/access.log
sudo tail -n 30 /var/log/nginx/error.log
```

He must answer after each command:

```text
What does this prove?
What does this not prove?
What is the next likely layer?
```

This is the skill: layer-by-layer investigation.

---

# Part 10 — Five-minute daily drills

Use these after each study session.

## Daily Drill A: Current boot summary

```bash
journalctl -b -n 30
```

Question:

> What are the most recent system events?

## Daily Drill B: SSH service

```bash
sudo journalctl -b _SYSTEMD_UNIT=sshd.service -n 20
```

Question:

> Any successful or failed logins?

## Daily Drill C: Authentication text log

```bash
sudo tail -n 20 /var/log/secure
```

Question:

> What recent security/authentication events are visible?

## Daily Drill D: Boot history

```bash
journalctl --list-boots
```

Question:

> When was the machine last booted?

## Daily Drill E: Service plus config

```bash
systemctl status firewalld
sudo journalctl -b _SYSTEMD_UNIT=firewalld.service -n 20
sudo firewall-cmd --list-all
```

Question:

> What is the difference between service state, service history, and firewall configuration?

---

# Completion test

Give him this problem:

```text
Someone says: “SSH is broken on app01.”
```

He should not immediately edit config.

He should run:

```bash
systemctl status sshd
ss -tulpn | grep ':22'
sudo firewall-cmd --list-all
sudo journalctl -b _SYSTEMD_UNIT=sshd.service --since "15 minutes ago"
sudo tail -n 30 /var/log/secure
ssh -v deploy@app01
```

Then explain:

1. Is `sshd` running?
    
2. Is it listening on port 22?
    
3. Is the firewall allowing SSH?
    
4. Did the login attempt reach the server?
    
5. Was the failure authentication, network, service, or permissions related?
    
6. Which evidence supports that conclusion?
    

That is the real lesson from the Red Hat chapter: logs are not decoration. They are evidence.