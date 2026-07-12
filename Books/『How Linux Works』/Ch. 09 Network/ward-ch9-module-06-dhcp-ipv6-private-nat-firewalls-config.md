#Book/How-Linux-Works #Author/Ward 
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

A home/office  linux machine gets  its configurations with DHCP from  the router. It uses a private IPv4 address. Traffic  to  the internet is forwarded by the router, that performs NAT. DNS resolves names. A firewall on the machine and router blocks inbound traffic and internet safety. 

## Observation commands

### Inspect current configuration

```bash
ip -br addr
ip route
cat /etc/resolv.conf
```
### NetworkManager if applicable

```bash
nmcli device status
nmcli connection show
```
### IPv6 awareness

```bash
ip -6 addr
ip -6 route
```
## Drills

### Drill 1: Configuration source

Find the machine's address, default gateway, and resolver. Decide which pieces may have come from DHCP.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 2: Private address identification

Identify whether the machine's IPv4 address is private. Explain how you know.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 3: Firewall concept check

Describe a case where routing works but a firewall still blocks traffic.

```text
Hypothesis:
Expected evidence:
Actual evidence:
Conclusion:
Next question:
```

### Drill 4: IPv6 recognition

Find one IPv6 address if present. Do not configure anything; just identify it.

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

Write an explanation: “How my home Linux machine gets an address and reaches the Internet.” Include DHCP, private address, default gateway, NAT, DNS, and firewall.

1.) On boot, the machine sends a DHCP request
2.) The router assigns a private IPv4 address, default gateway, and DNS servers.
3.) For outward internet traffic, packets to to the default gateway. The router performs NAT to map the private source IP to its public IP.
4.) DNS queries translate domain names to IPs.
5.) A local firewall allows connections and related replies while also blocking unwanted traffic. The router's firewall adds more protection. 

## Common traps

- Running commands without knowing what question they answer.
- Memorizing definitions but failing to interpret output.
- Jumping from one failed command to changing configuration.
- Saying “it works” or “it is broken” without naming what was actually tested.
