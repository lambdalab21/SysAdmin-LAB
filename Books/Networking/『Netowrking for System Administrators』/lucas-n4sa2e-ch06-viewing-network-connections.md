#Author/Lucas #Book/Network-for-System-Administrators #network 
# Chapter 6 — Viewing Network Connections

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

using OS tools to inspect addresses, routes, sockets, listening services, and active connections.

## Feynman analogy

A server is not a black box. It has gauges. `ip` and `ss` are gauges for addresses, routes, and conversations. The command is not the point; the question it answers is the point.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What command shows addresses?
- What command shows routes?
- What command shows listening sockets?

## While reading

Stop after each major section.

- For every table of output, identify the columns before interpreting rows.
- Ask whether the output shows configuration, current state, or live communication.
- If output feels overwhelming, narrow the question.

## After reading

Do these before moving on.

- Run `ip -br addr`, `ip route`, `ss -tulpn`, and `ss -tan`.
- Identify one listening socket and one established connection if present.
- Compare ordinary output with `sudo` output.

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

Take one line of `ss -tulpn` output and explain every important field.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Cheat sheet: question -> command -> what output proves -> what output does not prove.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
