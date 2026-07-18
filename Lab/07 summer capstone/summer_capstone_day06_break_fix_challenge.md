# Day 6 — Break/Fix Challenge Without Notes

## Expected time

```text
Target: 150–240 minutes
Minimum: 120 minutes
Deep version: 3–4 hours
```

## Goal

Troubleshoot by layer without immediately looking at notes.

Pattern:

```text
symptom
→ layer
→ evidence
→ fix
→ verification
```

This is the most important day of the capstone.


---

# Daily depth rule

Do not stop because the commands ran successfully.

Stop only when you can explain:

```text
What question was I asking?
What command did I run?
What exact evidence did I get?
What does it prove?
What does it not prove?
What is the next narrow question?
```

If you finish in under 60 minutes, you probably did it too shallowly. Add one extra break/fix case or rewrite your explanation more clearly.


# Challenge rules

For each failure:

```text
1. Write expected symptom.
2. Create the failure.
3. Observe symptom.
4. Identify layer.
5. Find evidence.
6. Fix.
7. Verify.
8. Write what you learned.
```

Do not look at old labs until after writing your first hypothesis and first evidence command.

# Failure 1 — Backend service stopped

Break:

```bash
sudo systemctl stop site6-app
```

Test:

```bash
curl -v -H 'Host: site6.local' http://app01/
```

Record:

```text
Symptom:
First command I used:
Best evidence:
Layer:
Fix:
Verification:
```

# Failure 2 — Wrong Nginx proxy port

Change proxy target to port 8999, reload Nginx, troubleshoot, then fix back to 8000.

Record:

```text
Symptom:
Evidence app was still running:
Evidence Nginx pointed wrong:
Layer:
Fix:
Verification:
```

# Failure 3 — Bad database ownership

Break:

```bash
sudo chown root:root /var/lib/site6-app
sudo systemctl restart site6-app
```

Try creating a task. Troubleshoot. Fix:

```bash
sudo chown -R site6:site6 /var/lib/site6-app
sudo systemctl restart site6-app
```

Record:

```text
Symptom:
Journal evidence:
Permission evidence:
Layer:
Fix:
Verification:
```

# Failure 4 — Wrong Host header

Run:

```bash
curl -v -H 'Host: wrong.local' http://app01/
curl -v -H 'Host: site6.local' http://app01/
```

Record:

```text
Difference:
What Host header controls:
How Nginx chooses a server block:
```

# Failure 5 — Browser name resolution problem

Use a fake name or temporarily remove `site6.local` from browser machine `/etc/hosts`.

Run:

```bash
getent hosts site6.local
curl -v http://site6.local/
curl -v -H 'Host: site6.local' http://app01/
```

Record:

```text
Name resolution evidence:
Which command still works:
What this proves:
```

# Deliverable

Create or improve:

```text
TROUBLESHOOTING.md
```

Required sections:

```text
# Troubleshooting

## General method
## 502 Bad Gateway
## App service stopped
## Wrong proxy port
## Database permission problem
## Name resolution problem
## Failed POST
## Commands by layer
```

# Completion checklist

```text
[ ] I completed at least 4 break/fix cases.
[ ] I wrote evidence before fixing.
[ ] I updated TROUBLESHOOTING.md.
[ ] I can explain symptom vs cause.
```
