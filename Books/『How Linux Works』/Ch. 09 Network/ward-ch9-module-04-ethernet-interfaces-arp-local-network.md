#Book/How-Linux-Works #Author/Ward 
#network
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
## Common traps

- Running commands without knowing what question they answer.
- Memorizing definitions but failing to interpret output.
- Jumping from one failed command to changing configuration.
- Saying “it works” or “it is broken” without naming what was actually tested.