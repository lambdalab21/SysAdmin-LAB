# Module 4 — Ethernet, Interfaces, ARP, and the Local Network

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- 9.9 The Physical Layer and Ethernet
- 9.10 Understanding Kernel Network Interfaces
- 9.18 Revisiting a Simple Local Network
- 9.26 Ethernet, IP, ARP, and NDP

## Main question

> After Linux decides that a destination is local, how does it deliver the frame to the correct device?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Apartment building analogy

An IP address gets the packet to the correct building or neighborhood. A MAC address is like the apartment-door label used inside the local building. ARP helps find the local door label.

### Classroom analogy

If the teacher says 'Elliot, take this paper,' everyone hears it, but only Elliot responds. On a local network, ARP asks, 'Who has this IP address?' and the correct device replies with its MAC address.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] The difference between an IP address and a MAC address.
- [ ] What a network interface is.
- [ ] What ARP does for IPv4.
- [ ] Why ARP is local-network behavior.
- [ ] Why cached neighbor entries can hide new ARP traffic.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- When looking at `ip link`, do not skip interface state. Identify which interface is actually usable.
- When using `ip neigh`, separate IP address, MAC address, interface, and neighbor state.
- If `tcpdump arp` shows nothing, do not panic. Ask whether the neighbor information was already cached.

## Observation commands

### Inspect interfaces and neighbors

```bash
ip link
ip neigh
ping -c 1 app01
ip neigh
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Observe ARP if possible

```bash
sudo tcpdump -ni any arp
# In another terminal:
ping -c 1 app01
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Interface inventory

List the active interface, its MAC address, and whether it is UP.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Neighbor table explanation

Run `ip neigh` before and after contacting `app01`. Explain any change.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: ARP capture

Try to capture ARP traffic. If nothing appears, explain caching as a hypothesis.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. What is a network interface?

<details>
<summary>Answer</summary>

A software-visible representation of a network connection such as Ethernet, Wi-Fi, or loopback.

</details>

### 2. What is a MAC address?

<details>
<summary>Answer</summary>

A link-layer hardware address used on local network segments.

</details>

### 3. What problem does ARP solve?

<details>
<summary>Answer</summary>

It maps an IPv4 address to a MAC address on the local network.

</details>

### 4. Why is ARP local?

<details>
<summary>Answer</summary>

It is used to deliver frames on the local link, not across the whole Internet.

</details>

### 5. What does `ip neigh` show?

<details>
<summary>Answer</summary>

Neighbor entries such as IP-to-MAC mappings and their states.

</details>

### 6. Why might a second ping not generate visible ARP traffic?

<details>
<summary>Answer</summary>

The neighbor entry may already be cached.

</details>

## Final deliverable

Explain to a 10-year-old why a computer needs both an IP address and a MAC address.

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
