#Author/Lucas #Book/Network-for-System-Administrators #network 
# Chapter 13 — Server Packet Filtering

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

packet filters and firewalls from the sysadmin troubleshooting viewpoint.

## Feynman analogy

A router decides where traffic should go. A firewall is the bouncer deciding whether traffic is allowed through the door. Do not confuse directions with permission.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What is the difference between port closed and port blocked?
- What service ports does your server intentionally expose?
- Why can firewall changes lock you out?

## While reading

Stop after each major section.

- Focus first on concepts: permit, deny, state, direction, interface, protocol, and port.
- Do not memorize syntax before understanding policy.
- Ask how you would prove the firewall is involved without guessing.

## After reading

Do these before moving on.

- List exposed services with `sudo ss -tulpn`.
- Check the firewall tool used by the OS, but do not change rules unless this is a controlled lab.
- Test from another host with `nc -vz`.

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

Scenario: `ss` shows nginx listening on port 80 locally, but `nc -vz app01 80` from `client01` times out. What evidence suggests firewall filtering?

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Safe firewall-change rules for yourself before touching any real server.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
