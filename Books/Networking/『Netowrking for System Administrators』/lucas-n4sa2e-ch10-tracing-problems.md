# Chapter 10 — Tracing Problems

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

end-to-end troubleshooting, narrowing failures, and communicating evidence clearly.

## Feynman analogy

Troubleshooting is not swinging a hammer at every part of the machine. It is following footprints. Every command should either confirm a footprint or show where the trail disappears.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What is your standard troubleshooting ladder?
- What evidence would convince you the problem is DNS?
- What evidence would convince you the problem is the application service?

## While reading

Stop after each major section.

- For every example, identify the first wrong assumption a beginner would make.
- Write the evidence chain before the conclusion.
- Notice how useful reports include source, destination, protocol, port, time, and observed output.

## After reading

Do these before moving on.

- Build one runbook for SSH failure and one for HTTP failure.
- Create a short escalation report to a network team.
- Review one old lab and rewrite the troubleshooting steps more clearly.

## Do-not-fool-yourself checklist

- [ ] I can explain the chapter in plain English without rereading the paragraph.
- [ ] I can connect the chapter to evidence from `client01`, `app01`, or `db01`.
- [ ] I can name one beginner mistake this chapter helps prevent.
- [ ] I can say what this chapter proves and what it does **not** prove.
- [ ] I did not confuse recognition with mastery.

## Minimal observation format

Use this once per chapter. This is not meant to become a large lab.

```text
Question I am testing:

Command, output, diagram, or evidence:

Expected evidence:

Actual evidence:

Conclusion:

What this does NOT prove:
```

## Stop-and-think questions

1. What problem does this chapter help diagnose?
2. What evidence would prove the chapter’s main idea on a real Linux machine?
3. What would a lazy beginner wrongly assume here?
4. How does this apply to `client01 -> app01 -> db01`?
5. What would you say to a network team if you had to escalate?

## Mini-task

Write a support report for: `client01 cannot curl http://app01, but ssh works`. Include commands and outputs you would collect.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Capstone: diagnose one intentionally broken service and produce a report with hypothesis, evidence, conclusion, and fix.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
