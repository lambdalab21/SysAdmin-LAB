A more natural phrase is: When he moves on to the networking section, what order should he read the resources by Julia Evans, Michael Lucas, Brian Ward, and William Shotts?

He should not read four networking resources cover to cover in sequence. That would create unnecessary repetition. Use a layered approach: curiosity first, basic tools second, mental model third, troubleshooting depth last.

## Recommended order

### 1. Julia Evans: _Networking! ACK!_

Start with the free zine _**Networking! ACK!**_.

It is about 20 pages, uses short comics, and begins with a concrete question: what happens when you download a picture from the Internet? Julia Evans describes her zines as short, informative, and fun comics designed to teach something useful quickly. ([wizard zines](https://wizardzines.com/zines/networking/?utm_source=chatgpt.com "Networking! ACK!"))

This should be casual reading. No quizzes. No notes. No labs unless he spontaneously wants to try something.

**Purpose:** create a rough mental map and make the topic inviting.

---

### 2. Shotts: _The Linux Command Line_, Chapter 16 — “Networking”

Read William Shotts next.

Chapter 16 of the current third edition is explicitly titled “Networking.” The book is organized around practical command-line tasks, including working with networking tools. ([nostarch.com](https://nostarch.com/linux-command-line-3e?utm_source=chatgpt.com "The Linux Command Line, 3rd Edition"))

Shotts is the right first formal reading because it introduces the commands from the user’s perspective. He can immediately run commands without needing to understand every layer of the network stack.

He should become comfortable with tools such as:

```bash
ping
traceroute
ip
ssh
scp
sftp
wget
curl
```

Depending on the edition, the exact list may differ slightly. That does not matter. The point is to start using the network rather than merely reading about it.

**Purpose:** learn the basic vocabulary and perform simple tasks.

---

### 3. Julia Evans: _Bite Size Networking_

Read _**Bite Size Networking**_ immediately after Shotts or alongside it.

This zine explains 17 important Linux networking tools. Each tool gets a one-page comic and a small set of useful options rather than an exhaustive manual-page-style explanation. ([wizard zines](https://wizardzines.com/zines/bite-size-networking/?utm_source=chatgpt.com "Bite Size Networking!"))

This is where he should start building a durable toolbox:

```bash
ping
curl
dig
ssh
nc
ss
tcpdump
openssl
```

Julia Evans also provides a free “Every Linux networking tool I know” poster. It includes tools such as `nc`, `socat`, `ss`, `lsof`, `fuser`, `iptables`, `nftables`, `ftp`, and `sftp`. ([wizard zines](https://wizardzines.com/comics/every-linux-networking-tool-i-know/?utm_source=chatgpt.com "every Linux networking tool I know"))

Do not require him to memorize the poster. It is a map, not a checklist.

**Purpose:** reinforce Shotts with memorable explanations and expose him to tools he will revisit for years.

---

### 4. Ward: _How Linux Works_, Chapter 9 — “Understanding Your Network and Its Configuration”

Now read Brian Ward carefully.

Chapter 9 is titled “Understanding Your Network and Its Configuration.” Ward moves beyond “run this command” and asks two foundational questions:

- How does a computer know where to send data?
    
- When the destination receives data, how does it know what it received?
    

([オライリー](https://www.oreilly.com/library/view/how-linux-works/9781457185519/ch09.html?utm_source=chatgpt.com "How Linux Works, 2nd Edition"))

This is the most important conceptual reading in the sequence. He should understand:

```text
interface
→ MAC address
→ IP address
→ subnet
→ route
→ default gateway
→ ARP or neighbor discovery
→ TCP or UDP
→ port
→ application
```

He should connect each concept to a command:

|Concept|Command|
|---|---|
|Interface and IP address|`ip addr`|
|Link state|`ip link`|
|Route and gateway|`ip route`|
|Neighbor table|`ip neigh`|
|Name resolution|`getent hosts`, `dig`|
|Listening services|`ss -tulpn`|
|Packet flow|`tcpdump`|

**Purpose:** build the mental model that makes the tools meaningful.

---

### 5. Ward: _How Linux Works_, Chapter 10 — “Network Applications and Services”

Continue directly into Ward Chapter 10.

The official table of contents places “Network Applications and Services” immediately after Chapter 9. ([nostarch.com](https://nostarch.com/howlinuxworks3?utm_source=chatgpt.com "How Linux Works, 3rd Edition"))

This chapter fits his current home lab particularly well because he has already worked with:

```text
SSH
nginx
curl
scp
rsync
app01
db01
```

Chapter 9 explains how packets get to a machine. Chapter 10 explains what happens after they arrive.

**Purpose:** connect network fundamentals to actual services.

---

### 6. Michael W. Lucas: _Networking for System Administrators_, 2nd edition

Read Lucas after Ward, not before.

Lucas wrote the book for system administrators who do not need to become full-time network engineers but do need enough networking knowledge to deploy applications, diagnose their own problems, and communicate intelligently with network specialists. The original edition covers TCP/IP, IPv6, troubleshooting tools, DNS, connectivity testing, and inspecting traffic. ([Google Books](https://books.google.com/books/about/Networking_for_Systems_Administrators.html?id=W18puwEACAAJ&utm_source=chatgpt.com "Networking for Systems Administrators - Michael W. Lucas"))

The substantially updated second edition is now available. Lucas stated that the new edition uses modern `ip` commands for Debian, adds PowerShell coverage for Windows, and includes TLS material. He also noted that DNS, `traceroute`, and `netcat` remain important. ([Mwl](https://mwl.io/archives/24087?utm_source=chatgpt.com "“Networking for System Administrators, 2nd Edition” Update"))

This is where he starts thinking like an administrator rather than a student:

```text
A website does not load.
Is DNS failing?
Is routing failing?
Is the port closed?
Is the firewall dropping packets?
Is nginx stopped?
Is TLS failing?
What evidence would distinguish these possibilities?
```

**Purpose:** develop a systematic troubleshooting method.

---

## Use Julia Evans again while reading Lucas

Julia Evans should not be treated as merely the introductory step. Return to the relevant zines as Lucas introduces each topic.

|Lucas topic|Julia Evans resource|
|---|---|
|Basic troubleshooting tools|_Bite Size Networking_|
|DNS|_How DNS Works_|
|Packet capture|`tcpdump` comics and posts|
|HTTP requests|HTTP and `curl` comics|
|Sockets|sockets comic|
|General command selection|“Every Linux networking tool I know” poster|

Her _**How DNS Works**_ zine is especially useful because DNS has difficult terminology, invisible background behavior, and caches at multiple levels. ([wizard zines](https://wizardzines.com/zines/dns/?utm_source=chatgpt.com "How DNS Works"))

---

## Compact reading plan

|Stage|Resource|Reading style|
|---|---|---|
|1|Julia Evans: _Networking! ACK!_|Casual preview|
|2|Shotts: Chapter 16|Read and run commands|
|3|Julia Evans: _Bite Size Networking_|Read alongside Shotts|
|4|Ward: Chapter 9|Read carefully; explain concepts|
|5|Ward: Chapter 10|Connect concepts to SSH, nginx, and other services|
|6|Lucas: _Networking for System Administrators_, 2nd edition|Read gradually with troubleshooting experiments|
|7|Julia Evans: DNS, HTTP, sockets, and `tcpdump` resources|Revisit when relevant|

## Where _Network Basics for Hackers_ fits

Keep _Network Basics for Hackers_ as optional recreational reading after Ward Chapter 9 begins. Do not make it part of the required sequence.

The core curriculum should be:

```text
Julia Evans
→ Shotts
→ Ward
→ Lucas
```

That order moves from enjoyment to commands, from commands to understanding, and from understanding to diagnosis. It should keep the networking section interesting without sacrificing the durable foundation.