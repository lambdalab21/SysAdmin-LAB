For Michael W. Lucas’s **_Networking for System Administrators_, 2nd edition**, read it cover to cover, but not all at once and not before Ward.

Lucas wrote it as a compact, practical overview for system administrators who need enough networking knowledge to deploy services, troubleshoot failures, and communicate with network specialists. It is not a large academic networking textbook. The official description emphasizes modern networks, TCP/IP, IPv6, diagnostic tools, TLS, packet inspection, connectivity testing, and troubleshooting from the physical connection through DNS. ([Michael W Lucas](https://mwl.io/static/books/networking-for-system-administrators-second-edition.html?utm_source=chatgpt.com "Networking for System Administrators, second edition"))

## Recommended sequence

### First: Shotts and Ward

Use these as the structured introduction:

```text
Shotts: networking chapter
→ Ward: How Linux Works, Chapter 9
→ Ward: How Linux Works, Chapter 10
```

Shotts gives him commands. Ward builds the mental model. By the end of Ward Chapter 10, he should understand enough to benefit from Lucas rather than merely encounter unfamiliar terminology.

### Then: read Lucas from beginning to end

Do not cherry-pick Lucas initially. The book’s value is the progression:

```text
network layers
→ Ethernet
→ IPv4 and IPv6
→ TCP/IP, ports, and connection state
→ diagnostic tools
→ packet capture
→ traffic filtering
→ DNS, TLS, and connectivity troubleshooting
```

The first edition followed this kind of practical progression, and the second edition updates the material with modern `ip` commands and adds TLS coverage. ([マタヒラ](https://camilomatajira.wordpress.com/2021/10/05/networking-for-system-administrators-it-mastery-book-5-by-michael-w-lucas/?utm_source=chatgpt.com "Book Review: Networking for System Administrators (IT ..."))

Skipping early chapters because he has already seen IP addresses or ports would be a mistake. Lucas is not merely defining terms. He is teaching a system administrator how to reason about failures.

## Read slowly, with small observations

Treat Lucas as a second pass through networking, not as a new lab course. He already has enough labs.

A reasonable pace is one chapter or section at a time, followed by one or two quick observations on his existing machines:

```bash
ip addr
ip route
ip neigh
ss -tulpn
dig app01
curl -v http://app01
sudo tcpdump -ni any port 80
```

The purpose is not to create additional assignments. It is to make the text attach itself to something he can see.

## Use Julia Evans alongside Lucas selectively

Julia Evans should not be read in a fixed sequence from beginning to end. Her zines and comics work better as visual supplements.

For example:

|Lucas topic|Julia Evans supplement|
|---|---|
|Basic command-line tools|_Bite Size Networking_|
|DNS|_How DNS Works_|
|Packet inspection|`tcpdump` zine or comics|
|HTTP|`curl` and HTTP comics|
|Sockets and ports|socket-related comics|
|General review|“Every Linux networking tool I know”|

That combination works well because Lucas gives him a coherent explanation, while Julia Evans gives him a memorable picture or quick reference.

## What not to do

Do not interleave four books page by page:

```text
Shotts section 1
→ Ward section 1
→ Lucas section 1
→ Julia Evans comic
→ repeat
```

That sounds thorough, but it fragments the subject and creates unnecessary repetition.

Use a cleaner progression:

```text
Julia Evans preview
→ Shotts networking chapter
→ Ward Chapters 9 and 10
→ Lucas cover to cover
→ Julia Evans selectively whenever a concept benefits from a visual explanation
```

Lucas should be the final consolidation book in this phase. After completing it, he should have a durable system-administrator-level networking foundation without being forced into an academic computer-networks curriculum too early.