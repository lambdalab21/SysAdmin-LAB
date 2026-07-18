#Book/How-Linux-Works #Author/Ward 
# Module 1 — Packets, Layers, and the Basic Mental Model

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- 9.1 Network Basics
- 9.2 Packets
- 9.3 Network Layers

## Main question

> What information must be added to data before it can travel across a network?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Mail analogy

Imagine sending a drawing to a friend through the mail. The drawing is the payload. The envelope has delivery information. The postal truck, sorting office, and local mailbox each solve different parts of the delivery problem. Network layers work similarly: each layer adds the information needed for its job.

### Nested boxes analogy

Application data is placed inside a transport-layer box, then inside an IP box, then inside an Ethernet/Wi-Fi box. The receiver opens the boxes in reverse order.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] What a packet is.
- [ ] The difference between a header and a payload.
- [ ] Why large messages are divided into packets.
- [ ] Why different layers add different headers.
- [ ] Why saying “the network is broken” is too vague.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- Before running each command, predict whether it is testing a host, a service, or a remote login.
- After running each command, write one sentence: “This command proves ___, but it does not prove ___.”
- Do not use the word “network” alone. Say which part: name lookup, route, host reachability, TCP connection, service, or application.

## Observation commands

### Generate simple network activity

```bash
ping -c 3 app01
curl -v http://app01
ssh app01
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Command classification

Classify `ping`, `curl`, and `ssh`: which uses ICMP, which uses TCP, and which reaches an application service?

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Layer explanation

Explain how application data becomes something that can travel across a local network.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: Failure reasoning

`ping app01` works but `curl http://app01` fails. Name three things that could still be broken.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. What is a packet?

<details>
<summary>Answer</summary>

A packet is a small unit of data sent across a network, usually containing headers and payload.

</details>

### 2. What is a header?

<details>
<summary>Answer</summary>

A header is control information added to data so network software and devices know how to handle it.

</details>

### 3. What is the payload?

<details>
<summary>Answer</summary>

The payload is the actual data being carried.

</details>

### 4. Why use layers?

<details>
<summary>Answer</summary>

Layers separate responsibilities so each layer solves one part of the communication problem.

</details>

### 5. What is encapsulation?

<details>
<summary>Answer</summary>

Wrapping higher-layer data inside lower-layer headers before transmission.

</details>

### 6. Why is “the network is down” a weak diagnosis?

<details>
<summary>Answer</summary>

It does not specify which layer or component failed.

</details>

## Final deliverable

Write a 10-year-old explanation: “What happens when I ask a computer to fetch a web page?” Use the words packet, header, payload, and layer.

## Self-grade

Grade yourself harshly. A weak explanation means the idea is not solid yet.

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the main idea without looking |  |  |  |  |
| I can connect the idea to command output |  |  |  |  |
| I can use the idea in troubleshooting |  |  |  |  |
| I can avoid vague words like 'network problem' |  |  |  |  |

## Common traps

- Running commands without knowing what question they answer.
- Memorizing definitions but failing to interpret output.
- Jumping from one failed command to changing configuration.
- Saying “it works” or “it is broken” without naming what was actually tested.
