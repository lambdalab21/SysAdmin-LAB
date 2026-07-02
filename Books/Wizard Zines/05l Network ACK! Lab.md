Use one client machine and `app01`.

### 1. Identify the machine’s network identity

```
ip -br addrip route
```

He should identify:

- active interface;
- IPv4 address;
- CIDR prefix;
- default gateway.

### 2. Resolve a name

```
getent hosts app01getent hosts example.comdig example.com
```

He should explain:

- hostname;
- IP address;
- local hostname resolution;
- DNS lookup.

### 3. Compare host reachability with service reachability

```
ping -c 3 app01nc -vz app01 22nc -vz app01 80
```

He should explain why:

```
ping succeeds
```

does not prove:

```
nginx is running
```

### 4. Observe the neighbor table

```
ip neighping -c 1 app01ip neigh
```

He should find the mapping between an IP address and a MAC address.

### 5. Watch packets

In one terminal:

```
sudo tcpdump -ni any icmp
```

In another:

```
ping -c 3 app01
```

Then repeat with DNS:

```
sudo tcpdump -ni any port 53
```

and:

```
dig example.com
```

### 6. Observe an HTTP request

On `app01`:

```
sudo tcpdump -ni any tcp port 80
```

From the client:

```
curl -v http://app01/
```

He should recognize:

```
TCP handshake→ HTTP request→ HTTP response
```