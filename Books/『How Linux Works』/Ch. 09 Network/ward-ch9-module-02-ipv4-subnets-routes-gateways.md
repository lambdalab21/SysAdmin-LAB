# Module 2 — IPv4, Subnets, CIDR, Routes, and Gateways

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- 9.4 The Internet Layer
- 9.4.1 Viewing IP Addresses
- 9.4.2 Subnets
- 9.4.3 Common Subnet Masks and CIDR Notation
- 9.5 Routes and the Kernel Routing Table
- 9.6 The Default Gateway

## Main question

> How does Linux decide where to send an IP packet?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Street address analogy

An IP address is like a street address. A subnet is like a neighborhood. If the destination is in your neighborhood, you deliver it directly. If it is outside the neighborhood, you send it to a main road: the default gateway.

### Map analogy

The routing table is the machine’s map. Linux compares the destination against the map and chooses the most specific matching route.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] The difference between an IP address and a subnet.
- [ ] What `/24`, `/16`, and `/8` mean at a basic level.
- [ ] Why a default gateway is needed.
- [ ] What a routing table is.
- [ ] Why local traffic and Internet traffic may use different next hops.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- Do not just copy the IP address. Label it: interface, address, prefix length, local network.
- Before `ip route get`, predict whether the destination should be local or via the gateway.
- When reading `ip route`, circle the default route first. Then identify more specific routes.

## Observation commands

### Observe addresses and routes

```bash
ip -br addr
ip route
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Ask Linux where it would send packets

```bash
ip route get 8.8.8.8
ip route get <app01-IP-address>
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Route reading

Find the default gateway and the interface used to reach it.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Local vs external

Compare `ip route get <app01-IP>` and `ip route get 8.8.8.8`. Explain the difference.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: CIDR reasoning

If an address is shown as `192.168.1.50/24`, explain what the `/24` means and why `192.168.1.1` is probably local.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. What is a subnet?

<details>
<summary>Answer</summary>

A subnet is a range of IP addresses treated as part of the same local network.

</details>

### 2. What does `/24` mean?

<details>
<summary>Answer</summary>

The first 24 bits identify the network portion of the IPv4 address.

</details>

### 3. What does the default gateway do?

<details>
<summary>Answer</summary>

It is the next hop used when no more specific route matches the destination.

</details>

### 4. What does `ip route` show?

<details>
<summary>Answer</summary>

The kernel routing table.

</details>

### 5. What does `ip route get 8.8.8.8` tell you?

<details>
<summary>Answer</summary>

The route Linux would use to reach that destination.

</details>

### 6. Why should he not overdo subnet arithmetic yet?

<details>
<summary>Answer</summary>

The immediate goal is routing intuition, not certification-style speed calculations.

</details>

## Final deliverable

Draw the path for a packet going from `client01` to `app01`, then from `client01` to `8.8.8.8`. Label the route decision.

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
