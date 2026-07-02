# Module 3 — ICMP, DNS, Hostname Resolution, and Localhost

**Source:** Brian Ward, *How Linux Works*, Chapter 9.

**Goal:** Understand the concept well enough to explain it, observe it on a Linux machine, and use it for troubleshooting.

## Reading scope

- 9.8 Basic ICMP and DNS Tools
- 9.8.1 ping
- 9.8.2 DNS and host
- 9.15 Resolving Hostnames
- 9.15.1 /etc/hosts
- 9.15.2 resolv.conf
- 9.15.3 Caching and Zero-Configuration DNS
- 9.15.4 /etc/nsswitch.conf
- 9.16 Localhost

## Main question

> How does a name become an address, and how do we test basic reachability?

Do not continue until you can answer this question in your own words.

## Feynman explanation

### Phonebook analogy

DNS is like asking a phonebook for a person’s phone number. The hostname is the name. The IP address is the number. But finding the number does not prove the person answered the phone.

### Nickname analogy

`/etc/hosts` is like a local nickname list. If your computer has a local note saying `app01` means `192.168.1.20`, it may use that before asking DNS.

### Explain it to a 10-year-old

Write a short explanation using ordinary words. No jargon unless you define it first.

```text
My explanation:

Words I used but need to define:

Where my explanation became vague:
```

## Must-understand checklist

- [ ] The difference between hostname resolution and reachability.
- [ ] What `ping` tests and what it does not test.
- [ ] The role of `/etc/hosts`.
- [ ] The role of `/etc/resolv.conf`.
- [ ] Why `getent hosts` is useful.
- [ ] What `localhost` and `127.0.0.1` refer to.

If any box feels fuzzy, reread that part. Do not reward yourself for simply reaching the end of the page.

## Slow-down checkpoints

- Before using `ping`, ask: am I testing a name or an IP address?
- If `ping app01` fails, immediately test `ping <app01-IP>` before making conclusions.
- Write the distinction: “Name lookup succeeded/failed” vs. “network reachability succeeded/failed.”

## Observation commands

### Resolve names

```bash
getent hosts app01
getent hosts example.com
host example.com
dig example.com
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Inspect resolution configuration

```bash
cat /etc/hosts
cat /etc/resolv.conf
grep '^hosts:' /etc/nsswitch.conf
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

### Test localhost

```bash
ping -c 3 127.0.0.1
ping -c 3 localhost
```

Before running the command, predict what kind of evidence it should produce. After running it, write what changed in your understanding.

## Drills

### Drill 1: Hostname vs IP

Run `getent hosts app01`, then `ping app01`, then `ping <app01-IP>`. Explain each result separately.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Local hosts experiment

Add a temporary `/etc/hosts` entry for `testbox`, resolve it with `getent hosts testbox`, remove it, then try again.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: Localhost explanation

Explain why `localhost` is useful even when the machine is offline.

**Discipline rule:** write the hypothesis first, then the evidence, then the conclusion.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

## Quiz

### 1. What does DNS do?

<details>
<summary>Answer</summary>

It maps names to records such as IP addresses.

</details>

### 2. What is `/etc/hosts`?

<details>
<summary>Answer</summary>

A local file that maps names to IP addresses.

</details>

### 3. What does `/etc/resolv.conf` usually identify?

<details>
<summary>Answer</summary>

The DNS resolver configuration, commonly the DNS server or local stub resolver.

</details>

### 4. Why use `getent hosts`?

<details>
<summary>Answer</summary>

It uses the system’s configured name-service order rather than only querying DNS directly.

</details>

### 5. What does `localhost` normally refer to?

<details>
<summary>Answer</summary>

The local machine, commonly 127.0.0.1 for IPv4.

</details>

### 6. Why can `ping app01` fail while `ping <app01-IP>` succeeds?

<details>
<summary>Answer</summary>

Hostname resolution may be broken while IP reachability still works.

</details>

## Final deliverable

Write a troubleshooting note for: “The browser works by IP address but not by hostname.” Include the first three commands you would run and why.

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
