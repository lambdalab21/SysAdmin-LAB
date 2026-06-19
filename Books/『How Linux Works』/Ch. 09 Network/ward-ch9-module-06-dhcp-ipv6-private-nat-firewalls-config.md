# Module 6 — DHCP, IPv6, Private Networks, NAT, Firewalls, and Configuration Tools

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- Careful: 9.19 Understanding DHCP
- Careful: 9.22 Private Networks (IPv4)
- Careful: 9.23 Network Address Translation
- Careful: 9.25 Firewalls
- Recognition: 9.7 IPv6 Addresses and Networks
- Recognition: 9.11–9.14 Network Configuration
- Recognition: 9.20 Automatic IPv6 Configuration
- Recognition: 9.21 Configuring Linux as a Router
- Recognition: 9.24 Routers and Linux
- Recognition: 9.27 Wireless Ethernet

## Main question

> How does a normal home or small-office Linux machine receive configuration and reach the Internet safely?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Hotel front desk analogy

DHCP is like the hotel front desk assigning you a room number, Wi-Fi information, and checkout instructions. Your computer joins a network and receives an address, gateway, and DNS information.

### Apartment NAT analogy

Many people in an apartment building share one street address. NAT is like the front desk keeping track of which inside apartment started which outside conversation.

### Security guard analogy

A firewall is like a security guard at a door. Routing decides where traffic goes. A firewall decides whether traffic is allowed.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] What DHCP can provide.
- [ ] Why private IPv4 addresses exist.
- [ ] What NAT does at a beginner level.
- [ ] The difference between a router and a firewall.
- [ ] Why IPv6 matters even if the lab mostly uses IPv4.
- [ ] Why network configuration managers exist.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- Separate 'I recognize this' from 'I can configure this.' Recognition is enough for some sections on the first pass.
- Do not memorize firewall syntax yet. First understand the difference between forwarding, filtering, and address translation.
- When reading IPv6, do not skip it because the current lab uses IPv4. Write one paragraph explaining why IPv6 exists.

## Observation commands

### Inspect current configuration

```bash
ip -br addr
ip route
cat /etc/resolv.conf
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### NetworkManager if applicable

```bash
nmcli device status
nmcli connection show
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### IPv6 awareness

```bash
ip -6 addr
ip -6 route
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Configuration source

Find the machine's address, default gateway, and resolver. Decide which pieces may have come from DHCP.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Private address identification

Identify whether the machine's IPv4 address is private. Explain how you know.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: Firewall concept check

Describe a case where routing works but a firewall still blocks traffic.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 4: IPv6 recognition

Find one IPv6 address if present. Do not configure anything; just identify it.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. What can DHCP provide?

<details>
<summary>Answer</summary>

An IP address, prefix/subnet information, default gateway, DNS servers, lease information, and other network options.

</details>

### 2. Why do home networks commonly use private IPv4 addresses?

<details>
<summary>Answer</summary>

Because private addresses can be reused inside local networks and are not routed directly on the public Internet.

</details>

### 3. What does NAT commonly do?

<details>
<summary>Answer</summary>

It translates private internal addresses to a public-facing address for outbound communication.

</details>

### 4. What is the difference between a router and a firewall?

<details>
<summary>Answer</summary>

A router forwards traffic between networks. A firewall permits or blocks traffic according to rules.

</details>

### 5. Why should IPv6 be recognized now?

<details>
<summary>Answer</summary>

Modern systems often have IPv6 enabled, and ignoring it can cause incomplete troubleshooting.

</details>

### 6. Why do network configuration managers exist?

<details>
<summary>Answer</summary>

They coordinate network settings automatically and reduce problems caused by manual boot-time configuration.

</details>

## Final deliverable

Write a one-page explanation: “How my home Linux machine gets an address and reaches the Internet.” Include DHCP, private address, default gateway, NAT, DNS, and firewall.

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
