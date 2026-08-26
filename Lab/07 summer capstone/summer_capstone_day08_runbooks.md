# Day 8 — Deployment and Backup Runbooks

---
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

# Part 2 — Backup/restore runbook

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

# Part 3 — Practice the runbook

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

# Part 4 — Pre-deployment checklist

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