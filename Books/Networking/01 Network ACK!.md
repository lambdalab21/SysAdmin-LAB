# Networking! ACK! — Short Networking Drills

These drills accompany Julia Evans' *Networking! ACK!* zine. They are deliberately short. Run one or two after reading, then reuse the recurring subset later.

## Safety and setup

- Use machines that belong to your home lab, such as `app01` and `db01`.
- Do not scan public hosts, school networks, neighbors' networks, or networks you do not administer.
- Replace `app01` with an IP address or hostname that resolves on your network when necessary.
- The `tcpdump` drills require `sudo`.

## Drill 1: Find the active interface and IP address

**Time:** 3 minutes

**Goal:** Connect the idea of an IP address to a real interface.

```bash
ip -br addr
ip link
```

**Task:** Identify the interface that is UP, its IPv4 address, and its CIDR prefix such as `/24`.

**Check your answer:** The answer should name the interface, address, and prefix. Explain that the IP address belongs to an interface, not vaguely to the whole computer.

## Drill 2: Find the default route

**Time:** 3 minutes

**Goal:** See where packets go when the destination is outside the local network.

```bash
ip route
```

**Task:** Identify the default gateway and the interface used to reach it.

**Check your answer:** The answer should point to the line beginning with `default via`. The gateway is the next hop for destinations that do not match a more specific route.

## Drill 3: Interpret CIDR notation

**Time:** 4 minutes

**Goal:** Connect a prefix such as `/24` to the size of an IPv4 network.

```bash
ip -br addr
```

**Task:** Find the IPv4 prefix on the active interface. For a `/24`, calculate the number of total addresses. For a different prefix, calculate `2^(32-prefix)`.

**Check your answer:** A `/24` has 256 total IPv4 addresses. Do not automatically claim that all 256 are assignable to hosts.

## Drill 4: Resolve a name to an IP address

**Time:** 4 minutes

**Goal:** Observe DNS name resolution.

```bash
getent hosts example.com
dig example.com
```

**Task:** Compare the results. Identify at least one IP address returned by DNS.

**Check your answer:** `getent` uses the system name-service configuration. `dig` directly shows DNS records and details such as TTL values.

## Drill 5: Query a specific DNS resolver

**Time:** 4 minutes

**Goal:** Distinguish the configured resolver from a resolver chosen explicitly.

```bash
dig example.com
dig @8.8.8.8 example.com
```

**Task:** Compare the `SERVER:` lines and returned records. If the second query is blocked, record that result.

**Check your answer:** The second command asks Google's public DNS resolver directly. A timeout can reveal that direct external DNS queries are blocked by the network.

## Drill 6: Trace DNS delegation

**Time:** 5 minutes

**Goal:** See that DNS is a distributed system.

```bash
dig +trace example.com
```

**Task:** Identify the root, top-level-domain, and authoritative stages. If the command cannot complete, explain where it stopped.

**Check your answer:** The output should show a chain of referrals rather than one resolver simply returning a cached answer.

## Drill 7: Inspect listening TCP and UDP sockets

**Time:** 4 minutes

**Goal:** Connect ports to processes that are waiting for network traffic.

```bash
ss -tulpn
sudo ss -tulpn
```

**Task:** Find the listening SSH socket. On `app01`, also find nginx if it is running. Compare the output with and without `sudo`.

**Check your answer:** A listening socket is a local endpoint waiting for connections. `sudo` may reveal process names and PIDs that are hidden from an ordinary user.

## Drill 8: Test a service by port

**Time:** 4 minutes

**Goal:** Separate host reachability from service reachability.

```bash
ping -c 3 app01
nc -vz app01 22
nc -vz app01 80
```

**Task:** Record which tests succeed. Explain why a successful ping does not prove that nginx is running.

**Check your answer:** `ping` tests ICMP reachability. `nc` tests whether a TCP connection can be established to a specific port.

## Drill 9: Compare an open port with a closed port

**Time:** 4 minutes

**Goal:** Observe that different network failures have different meanings.

```bash
nc -vz app01 22
nc -vz app01 65000
```

**Task:** Compare the messages from an expected open port and an expected unused port.

**Check your answer:** An open port accepts the TCP connection. An unused port commonly produces `connection refused`, meaning the host responded but no service accepted the connection.

## Drill 10: Send an HTTP request manually

**Time:** 5 minutes

**Goal:** See that HTTP/1.1 is a text protocol carried over TCP.

```bash
printf 'GET / HTTP/1.1\r\nHost: app01\r\nConnection: close\r\n\r\n' | nc app01 80
```

**Task:** Identify the HTTP status line and at least one response header.

**Check your answer:** The response should begin with a status line such as `HTTP/1.1 200 OK`. The `Host` header tells a server which site is being requested.

## Drill 11: Inspect the local neighbor table

**Time:** 4 minutes

**Goal:** Observe the mapping between local IP addresses and link-layer addresses.

```bash
ip neigh
ping -c 1 app01
ip neigh
```

**Task:** Compare the neighbor table before and after pinging `app01`. Find the IP address and MAC address associated with `app01` if it is on the same local network.

**Check your answer:** `ip neigh` is the modern Linux command for viewing neighbor entries. For IPv4 on a local network, ARP is used to learn the MAC address.

## Drill 12: Observe ICMP packets

**Time:** 5 minutes

**Goal:** Use packet capture as evidence.

```bash
# Terminal 1
sudo tcpdump -ni any icmp

# Terminal 2
ping -c 3 app01
```

**Task:** Identify echo requests and echo replies. Match each request with a reply.

**Check your answer:** The capture should show ICMP echo request and echo reply packets. This is the traffic generated by `ping`.

## Drill 13: Observe DNS packets

**Time:** 5 minutes

**Goal:** See a DNS request and response directly.

```bash
# Terminal 1
sudo tcpdump -ni any port 53

# Terminal 2
dig example.com
```

**Task:** Identify the query and response. Note whether UDP or TCP appears in the capture.

**Check your answer:** A simple DNS lookup commonly uses UDP port 53. DNS can also use TCP in some situations, so do not turn `usually UDP` into `always UDP`.

## Drill 14: Observe an HTTP conversation

**Time:** 5 minutes

**Goal:** Connect TCP packets to an application request.

```bash
# Terminal 1 on app01
sudo tcpdump -ni any tcp port 80

# Terminal 2 on another machine
curl -v http://app01/
```

**Task:** Find the TCP handshake and the later packets carrying the HTTP request and response.

**Check your answer:** Look for `SYN`, `SYN-ACK`, and `ACK` before application data is exchanged. HTTP traffic follows after the TCP connection is established.

## Drill 15: Save a packet capture for Wireshark

**Time:** 6 minutes

**Goal:** Separate packet capture from packet analysis.

```bash
# Terminal 1 on app01
sudo tcpdump -ni any -w web-request.pcap tcp port 80

# Terminal 2
curl http://app01/

# Stop tcpdump with Ctrl-C, then open web-request.pcap in Wireshark.
```

**Task:** In Wireshark, filter with `tcp.port == 80`. Find the handshake and use `Follow TCP Stream` if available.

**Check your answer:** The `.pcap` file contains captured packets. Wireshark provides a graphical way to inspect the same evidence recorded by `tcpdump`.

## Drill 16: Inspect a TLS handshake

**Time:** 5 minutes

**Goal:** Observe encrypted web traffic without pretending that the payload is readable.

```bash
openssl s_client -connect example.com:443 -servername example.com </dev/null
```

**Task:** Identify the server certificate information and the negotiated TLS version. Explain why the application data is not shown as ordinary readable HTTP.

**Check your answer:** TLS protects the application data in transit. The handshake negotiates security parameters and presents certificate information.

## Five-minute recurring subset

Repeat these after a week, then again after a month:

1. Find the active interface and IP address.
2. Find the default route.
3. Resolve a name with `getent` and `dig`.
4. Inspect listening sockets with `ss -tulpn`.
5. Test a host and a specific port with `ping` and `nc`.
6. Inspect the neighbor table with `ip neigh`.
7. Capture ICMP, DNS, or HTTP traffic with `tcpdump`.

## Modern Linux note

Older networking material may use `arp -na`. On a modern Linux system, prefer:

```bash
ip neigh
```

Use the term **TLS** for modern encrypted connections. **SSL** is the historical predecessor.