#Author/Lucas #Book/Network-for-System-Administrators #network 
# Chapter 5 — TCP/IP

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

transport-layer behavior, TCP, UDP, ports, sockets, connection state, and service reachability.

## Feynman analogy

An IP address gets you to the apartment building. A port gets you to the correct apartment. TCP is a careful conversation with acknowledgments. UDP is more like dropping off a note without a long conversation.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What port does SSH usually use?
- What port does HTTP usually use?
- Why can `ping` work while SSH fails?

## While reading

Stop after each major section.

- Whenever a port appears, ask which service should be listening there.
- Separate host reachability from service reachability.
- Do not reduce TCP versus UDP to `TCP good, UDP bad`; ask what each is for.

## After reading

Do these before moving on.

- Run `sudo ss -tulpn`.
- Find SSH and a web service if present.
- Use `nc -vz app01 22` and `nc -vz app01 80` to test specific services.

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

Diagnose: `ping app01` works, `nc -vz app01 22` succeeds, `nc -vz app01 80` fails. What is proven and what is not?

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Mini runbook: checking whether a service is reachable by port.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
