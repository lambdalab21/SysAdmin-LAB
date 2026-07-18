#Book/How-Linux-Works aAuthor/#Author/Ward 
# Module 5 — TCP, UDP, Sockets, Ports, and Services

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- 9.17 The Transport Layer: TCP, UDP, and Services
- 9.17.1 TCP Ports and Connections
- 9.17.2 UDP

## Main question

> Once a packet reaches the correct computer, how does Linux deliver it to the correct application?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Apartment building analogy

The IP address gets you to the building. The port number gets you to the correct apartment. The protocol says whether you are using a reliable conversation like TCP or a lighter message style like UDP.

### Reception desk analogy

A listening socket is like a reception desk waiting for visitors. If nobody is at the desk, visitors may reach the building but still fail to get service.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] Why ports exist.
- [ ] The difference between TCP and UDP at a beginner level.
- [ ] What a listening socket is.
- [ ] Why `ping` can succeed while `curl` fails.
- [ ] The difference between a refused connection and a timeout.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- Before testing a port, state which service you expect to answer.
- Do not say 'the server is down' when only one port failed.
- After every failed connection, classify it: name problem, route problem, host reachability problem, port problem, service problem, or application problem.

## Observation commands

### Inspect services and ports

```bash
ss -tulpn
sudo ss -tulpn
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Test common ports

```bash
nc -vz app01 22
nc -vz app01 80
nc -vz app01 65000
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Inspect HTTP behavior

```bash
curl -v http://app01
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Observe TCP traffic

```bash
sudo tcpdump -ni any tcp port 80
# In another terminal:
curl http://app01
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Listening socket hunt

Find SSH and nginx in `sudo ss -tulpn`. Record protocol, local address, port, and process.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Port test ladder

Run `ping`, then `nc -vz app01 22`, then `nc -vz app01 80`. Explain why each test adds evidence.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: Failure comparison

Compare an open port with an unused high port such as 65000. Explain the difference.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. Why does a computer need ports?

<details>
<summary>Answer</summary>

Ports identify which service or application endpoint should receive traffic.

</details>

### 2. What is a listening socket?

<details>
<summary>Answer</summary>

A socket waiting for incoming connections or traffic.

</details>

### 3. Are TCP port 53 and UDP port 53 the same endpoint?

<details>
<summary>Answer</summary>

No. TCP and UDP are different transport protocols.

</details>

### 4. What does TCP provide that UDP does not?

<details>
<summary>Answer</summary>

TCP provides reliable ordered byte-stream delivery with connection state.

</details>

### 5. What does connection refused usually suggest?

<details>
<summary>Answer</summary>

The host responded, but no service accepted the connection on that port or a rule rejected it.

</details>

### 6. Why can `ping` succeed while `curl` fails?

<details>
<summary>Answer</summary>

ICMP reachability can work while TCP port 80, nginx, firewall, or the application fails.

</details>

## Final deliverable

Write a mini runbook for: `ping app01` works, but `curl http://app01` fails. Include the next five commands and what each one tests.

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
