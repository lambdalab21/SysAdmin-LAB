# Chapter 2 — Ethernet

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

local-network delivery, Ethernet frames, MAC addresses, switches, and local-link behavior.

## Feynman analogy

Ethernet is like handing notes inside one classroom. You do not need a national mailing address; you need to know which person in the room should receive the note. Ethernet is local delivery.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What is a MAC address?
- How is a switch different from a router?
- What does `local network` mean?

## While reading

Stop after each major section.

- Ask whether each idea is about local delivery or routing between networks.
- Do not confuse MAC addresses with IP addresses.
- Sketch where `client01`, `app01`, and the home router sit.

## After reading

Do these before moving on.

- Run `ip link` and identify the active interface and MAC address.
- Run `ip neigh` and identify one neighbor entry if available.
- Explain why Ethernet matters even when most commands show IP addresses.

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

Draw `client01 -> switch/router -> app01`. Label where MAC addresses matter and where IP addresses matter.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Explain to a 10-year-old why local delivery needs a different kind of address from Internet delivery.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
