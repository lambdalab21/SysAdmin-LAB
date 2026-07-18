#Author/Lucas #Book/Network-for-System-Administrators #network 
# Chapter 3 — IPv4

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

IPv4 addresses, prefixes, subnets, private addresses, routes, and gateway decisions.

## Feynman analogy

IPv4 is like street addressing. The address identifies a place, and the prefix tells you the neighborhood. If the destination is inside your neighborhood, deliver directly. If it is outside, send it through the main road: the gateway.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What is your machine’s IPv4 address?
- What is your default gateway?
- What does `/24` mean at a beginner level?

## While reading

Stop after each major section.

- Every time an address appears, decide whether it is a host address, network range, or gateway.
- Do not turn subnetting into arithmetic theater. Tie it to route decisions.
- Connect private addresses to home networks and NAT, but do not pretend NAT is magic.

## After reading

Do these before moving on.

- Run `ip -br addr`, `ip route`, and `ip route get 8.8.8.8`.
- Identify your local subnet and default gateway.
- Write why a private IPv4 address is not directly reachable from the public Internet.

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

Given `192.168.1.50/24`, explain whether `192.168.1.20` is likely local and whether `8.8.8.8` is local.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Draw two paths: `client01 -> app01` and `client01 -> example.com`. Label local delivery versus gateway delivery.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
