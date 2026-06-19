# Chapter 11 — Packet Sniffing

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## What this chapter is about

packet capture as evidence: tcpdump, Wireshark, and observing what traffic actually happened.

## Feynman analogy

Packet sniffing is like putting a camera at a doorway. You are not guessing who entered; you are watching who actually passed through. But a camera pointed at the wrong door proves nothing.

Now write where the analogy breaks:

```text
The analogy helps because:

The analogy is incomplete because:

The real networking concept means:
```

## Before reading

Answer these before opening the chapter.

- What traffic do you expect when you run `ping`?
- What traffic do you expect when you run `curl`?
- What interface should you capture on?

## While reading

Stop after each major section.

- Do not admire packet output. Ask what question the capture answers.
- Separate capture filters from display filters if Wireshark is used.
- Ask whether traffic is missing, blocked, refused, encrypted, or misunderstood.

## After reading

Do these before moving on.

- Capture ICMP with `tcpdump`.
- Capture DNS traffic on port 53.
- Capture HTTP traffic to `app01` if nginx is running.
- Save one `.pcap` and open it in Wireshark if available.

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

Run `sudo tcpdump -ni any icmp`, then `ping -c 3 app01`. Explain request and reply.

Use this format:

```text
Hypothesis:
Evidence:
Conclusion:
Beginner mistake to avoid:
```

## Final deliverable

Note: `When packet capture is useful, when it is overkill, and how it can mislead me.`

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the idea simply |  |  |  |  |
| I can connect it to evidence |  |  |  |  |
| I can use it to troubleshoot |  |  |  |  |
| I know what not to overclaim |  |  |  |  |

## Rule for this chapter

Do not move on because the prose felt easy. Move on only when you can explain, observe, and avoid the beginner trap.
