#Book/The-Linux-Command-Line  #Author/Shotts 
#network 
# Author/Shotts Chapter 16 — Networking Drills

These drills accompany Chapter 16, **Networking**, of William Author/Shotts' *The Linux Command Line*.
Use machines that belong to your home lab, such as `app01` and `db01`.

## Safety and setup

- Do not scan public hosts, school networks, neighbors' networks, or networks you do not administer.
- Replace `app01`, `db01`, and `<user>` with actual hostnames, IP addresses, and usernames from the lab.
- Some commands may need to be installed separately depending on the distribution.
- Prefer `tracepath` if `traceroute` is unavailable.
- Prefer `ss` for normal work. Use `netstat` only as a historical comparison if it is already installed.
- Read the `ftp` section for historical context, but do not set up or use plaintext FTP for normal file transfer.

---

---

# Part 1 — Examine and monitor the network

## Drill 1: Verify local TCP/IP operation

**Time:** 3 minutes

```bash
ping -c 4 localhost
ping -c 4 127.0.0.1
```

**Task:**
Confirm that both commands work. Explain what this test verifies and what it does **not** verify.

**Check your answer:**
Both commands test the local loopback interface. Success does not prove that the Ethernet or Wi-Fi interface, router, DNS server, or Internet connection is working.

---

## Drill 2: Test another machine on the LAN

**Time:** 4 minutes

```bash
ping -c 4 app01
ping -c 4 <app01-IP-address>
```

**Task:**
Compare the hostname-based test with the IP-address-based test.

**Questions:**

1. If both succeed, what has been verified? Hostname resolution and LAN both work. 
2. If the IP-address test succeeds but the hostname test fails, what should you investigate? Name Resolution should be investigated. 
3. Can a failed `ping` prove that the host is down? No, firewalls can block ICMP while the host is up.

**Check your answer:**
A successful hostname-based ping shows that name resolution and ICMP reachability both work. If the IP-address test works but the hostname test fails, investigate hostname resolution. A failed ping does not prove that the host is down because firewalls and hosts can block ICMP traffic.

---

## Drill 3: Interpret ping statistics

**Time:** 4 minutes

```bash
ping -c 10 app01
```

**Task:**
Record:

- packets transmitted;
- packets received;
- packet-loss percentage;
- minimum, average, and maximum round-trip time.

**Question:**
Which result would concern you more on a wired LAN: a slightly changing response time or packet loss?

**Check your answer:**
Packet loss on a small wired LAN deserves investigation. Small round-trip-time variation is usually less concerning.

---

## Drill 4: Trace the path to a destination

**Time:** 5 minutes

Try one of these:

```bash
tracepath example.com
```

or:

```bash
traceroute example.com
```

**Task:**
Identify:

- the first hop;
- the number of visible hops;
- any lines containing `*`;
- the final destination, if reached.

**Questions:**

1. What is the first hop likely to be on a home network? The local router. 
2. Does `* * *` automatically prove that the route is broken? No. * * * only means that the router had not responded to the probe. Traffic may still pass. *

**Check your answer:**
The first hop is commonly the local router or default gateway. Asterisks can mean that a router did not answer the diagnostic probe; they do not automatically prove that traffic cannot pass through it.

---

## Drill 5: List interfaces and addresses

**Time:** 4 minutes

```bash
ip -br addr
ip link
```

**Task:**
Identify:

- the loopback interface;
- the active Ethernet or Wi-Fi interface;
- its IPv4 address;
- its CIDR prefix;
- whether the interface is `UP`.

**Check your answer:**
`lo` is the loopback interface. The active network interface should normally have an address and show an enabled state.

---

## Drill 6: Inspect the routing table

**Time:** 5 minutes

```bash
ip route
```

**Task:**
Find:

- the local-network route;
- the default route;
- the default gateway;
- the interface used by the default route.

Then run:

```bash
ip route get <app01-IP-address>
ip route get 8.8.8.8
```

**Question:**
Why may the route to `app01` differ from the route to `8.8.8.8`?

**Check your answer:**
A host on the same LAN can usually be reached directly through the local interface. An external address normally goes through the default gateway.

---

## Drill 7: Inspect listening sockets with the modern tool

**Time:** 5 minutes

```bash
ss -tulpn
sudo ss -tulpn
```

**Task:**
Find:

- the SSH service;
- nginx or another web service, if one is running;
- the protocol;
- the local address;
- the port;
- the process name, where visible.

**Questions:**

1. Why may `sudo` reveal more process information?
2. What is the difference between a listening socket and an established connection?

**Check your answer:**
Ordinary users may not be allowed to see complete process details for every socket. A listening socket waits for incoming connections; an established connection represents an active conversation.

---

## Drill 8: Compare `ss` with historical `netstat`

**Time:** 5 minutes

Run this only if `netstat` is already installed:

```bash
netstat -tulpn
netstat -r
```

Compare with:

```bash
ss -tulpn
ip route
```

**Task:**
Map the older command to its modern replacement.

| Older command | Modern replacement |
|---|---|
| `netstat -tulpn` | `ss -tulpn` |
| `netstat -r` | `ip route` |

**Question:**
Why is it still useful to recognize `netstat` even if you normally use `ss` and `ip`?

**Check your answer:**
Older documentation, scripts, systems, and troubleshooting discussions still refer to `netstat`. Recognition is useful; habitual use is not necessary.

---
