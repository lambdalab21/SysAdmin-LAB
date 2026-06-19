# Chapter 12 — Creating Traffic

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

using tools such as curl, nc/ncat, and openssl to generate controlled network traffic.

## Feynman analogy

If you want to test a doorbell, you must press the doorbell. Creating traffic is pressing a specific doorbell on purpose so you can watch what happens.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What traffic does `curl` create?
- What does `nc -vz` test?
- Why create traffic instead of waiting for users to complain?

## While reading

Stop after each major section.

- For each traffic-generation tool, ask: protocol, destination, port, and expected response.
- Do not use a tool on networks you do not administer.
- Prefer small controlled traffic over noisy broad scans.

## After reading

Do these before moving on.

- Use `curl -v http://app01`.
- Use `nc -vz app01 22` and `nc -vz app01 80`.
- Use `openssl s_client -connect example.com:443 -servername example.com </dev/null` to observe TLS setup.

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

Generate an HTTP request and capture it. State what you expected before running the commands.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Table: tool -> traffic generated -> question answered -> risk or limitation.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
