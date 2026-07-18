# Day 8 — Deployment and Backup Runbooks

## Expected time

```text
Target: 120–180 minutes
Minimum: 90 minutes
Deep version: 3 hours by actually following both runbooks
```

## Goal

Turn understanding into repeatable procedures.

A runbook is a clear procedure you can follow during a real task.

Today polish:

```text
DEPLOYMENT.md
BACKUP_RESTORE.md
```


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


# Part 1 — Deployment runbook

`DEPLOYMENT.md` should include:

```markdown
# Deployment Runbook

## Goal
## Preconditions
## 1. Check Git status
## 2. Inspect diff
## 3. Commit intentionally
## 4. Back up database
## 5. rsync dry run
## 6. Deploy code
## 7. Restart service
## 8. Verify HTTP
## 9. Verify logs
## 10. Confirm data survived
## Rollback notes
```

# Part 2 — Include exact commands and explanations

For each command include:

```text
Command:
What it proves:
What it does not prove:
Risk if wrong:
```

# Part 3 — Backup/restore runbook

`BACKUP_RESTORE.md` should include:

```markdown
# Backup and Restore Runbook

## What is protected
## Live database path
## Backup directory
## Simple backup procedure
## Restore-test procedure
## Real restore procedure
## Verification
## Risks
```

# Part 4 — Practice the runbook

Actually follow your runbook once.

Do not use memory.

Use only your written instructions.

If the runbook is unclear, fix it.

Write:

```text
Step that was unclear:
Correction made:
Evidence that runbook now works:
```

# Part 5 — Pre-deployment checklist

Add to `DEPLOYMENT.md`:

```text
[ ] git status checked
[ ] git diff inspected
[ ] database backed up
[ ] backup file exists
[ ] rsync dry run inspected
[ ] dry run does not touch /var/lib/site6-app
[ ] code deployed
[ ] service restarted
[ ] curl verifies page
[ ] old data still appears
[ ] Nginx access log checked
[ ] Nginx error log checked
[ ] app journal checked
```

# Completion checklist

```text
[ ] DEPLOYMENT.md is usable.
[ ] BACKUP_RESTORE.md is usable.
[ ] Commands include explanations.
[ ] I followed the runbook once.
[ ] I fixed unclear steps.
```
