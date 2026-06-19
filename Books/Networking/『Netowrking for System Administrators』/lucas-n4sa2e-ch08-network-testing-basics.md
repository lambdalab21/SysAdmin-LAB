# Chapter 8 — Network Testing Basics

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

testing reachability, paths, ports, latency, and connectivity without overclaiming.

## Feynman analogy

Testing a network is like checking why a package did not arrive. First check the address, then the driveway, then the road, then whether the building accepts deliveries. One test never proves everything.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What does `ping` actually test?
- What does `tracepath` or `traceroute` show?
- What does `nc -vz` test?

## While reading

Stop after each major section.

- After every test, write: `This proves ___, but it does not prove ___`.
- Do not treat ICMP failure as proof that the host is down.
- Watch for the difference between no response, refusal, and application error.

## After reading

Do these before moving on.

- Test `localhost`, `app01`, and an external IP.
- Test a specific TCP port with `nc`.
- Use `curl -v` to observe a higher-level request.

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

Fill in the evidence chain: name lookup -> route -> host reachability -> port reachability -> application response.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Troubleshooting ladder for `client01 cannot reach http://app01`.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
