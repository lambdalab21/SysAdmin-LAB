# Follow-Up: `/etc/hosts`, DNS, and How Names Become IP Addresses

## Purpose

You have been using names like:

```text
app01
site1.local
site2.local
site3.local
site4.local
```

You also learned that a browser cannot easily do this:

```bash
curl -H 'Host: site2.local' http://app01/
```

Instead, the browser should open:

```text
http://site2.local/
```

For that to work, your computer must know:

```text
What IP address does site2.local mean?
```

This lesson explains:

```text
/etc/hosts
DNS
hostname resolution
Host header
Nginx server_name
```

Do not rush. This is a core networking idea.

---

# Big idea

Before a browser can connect to a website, it needs an IP address.

Humans like names:

```text
site2.local
example.com
app01
```

Computers connect to IP addresses:

```text
192.168.0.107
93.184.216.34
```

So the computer must translate:

```text
name → IP address
```

That process is called **name resolution**.

---

# Feynman version

Explain it to a younger student:

```text
A hostname is like a person's name.
An IP address is like the person's street address.

If I say "go to site2.local,"
the computer first asks,
"Where does site2.local live?"
```

`/etc/hosts` is like a small address book on your own computer.

DNS is like a large address lookup system used by many computers.

---

# Part 1 — What `/etc/hosts` is

`/etc/hosts` is a local file on a machine.

It maps names to IP addresses.

Example:

```text
192.168.0.107 app01 site1.local site2.local site3.local site4.local
```

This means:

```text
app01       → 192.168.0.107
site1.local → 192.168.0.107
site2.local → 192.168.0.107
site3.local → 192.168.0.107
site4.local → 192.168.0.107
```

## Important

`/etc/hosts` affects the machine where the file exists.

If your browser runs on your laptop, then the laptop's `/etc/hosts` matters.

If you edit `/etc/hosts` on `app01`, that does not automatically teach your laptop browser what `site2.local` means.

## Stop and think

Answer:

```text
Which machine has the browser?

Which machine's /etc/hosts should I edit?

What IP address should site2.local point to?

What does /etc/hosts do?
```

Expected:

```text
Edit /etc/hosts on the browser machine.
Map site2.local to app01's IP address.
```

---

# Part 2 — What DNS is

DNS stands for **Domain Name System**.

DNS also maps names to IP addresses.

Example:

```text
www.example.com → 93.184.216.34
```

But DNS is not just one file.

It is a distributed lookup system.

Feynman version:

```text
/etc/hosts is a small address book on one computer.
DNS is more like a shared phone book system for networks and the internet.
```

## Is DNS basically a global hosts file?

Partly, but not exactly.

Good simple answer:

```text
DNS solves the same kind of problem as /etc/hosts:
name → IP address.
```

But DNS is not literally one global file.

DNS is:

```text
distributed
hierarchical
cached
managed by many different servers and organizations
```

So do not say:

```text
DNS is one big global hosts file.
```

Say:

```text
DNS is a distributed system that does name-to-IP lookup, similar in purpose to /etc/hosts but much more scalable.
```

---

# Part 3 — Lookup order: hosts file vs DNS

When you run:

```bash
getent hosts site2.local
```

Linux checks the system's name-service configuration.

Usually it may check:

```text
/etc/hosts first
then DNS
```

The exact order is controlled by:

```text
/etc/nsswitch.conf
```

You do not need to master that file yet.

For now, understand:

```text
If site2.local is in /etc/hosts, the computer can resolve it without asking DNS.
```

---

# Part 4 — Observe name resolution

Run:

```bash
getent hosts app01
```

Write:

```text
Command:

Output:

What name was looked up?

What IP address was returned?

What this proves:
```

Now run:

```bash
getent hosts site2.local
```

If nothing appears, your computer does not know that name yet.

After adding it to `/etc/hosts`, run again:

```bash
getent hosts site2.local
```

Expected example:

```text
192.168.0.107 site2.local
```

Write:

```text
Before /etc/hosts entry:

After /etc/hosts entry:

What changed?

What this proves:
```

---

# Part 5 — Add lab site names to `/etc/hosts`

On the computer running the browser:

```bash
sudo vi /etc/hosts
```

Add one line, using your real `app01` IP address:

```text
192.168.0.107 app01 site1.local site2.local site3.local site4.local
```

Or use separate lines:

```text
192.168.0.107 app01
192.168.0.107 site1.local
192.168.0.107 site2.local
192.168.0.107 site3.local
192.168.0.107 site4.local
```

Both are acceptable.

## Stop before saving

Write:

```text
Machine I am editing:

IP address I am using:

Names I am adding:

Why this is needed:
```

Green check:

```text
[ ] I am editing the browser machine's /etc/hosts.
[ ] I used app01's IP address.
[ ] I added the site names I want the browser to open.
[ ] I understand this is local to this machine.
```

---

# Part 6 — Test before opening the browser

Run:

```bash
getent hosts site2.local
```

Then:

```bash
curl -v http://site2.local/
```

This is different from:

```bash
curl -H 'Host: site2.local' http://app01/
```

## Compare

Command A:

```bash
curl -H 'Host: site2.local' http://app01/
```

Means:

```text
Connect to app01.
Send Host: site2.local.
```

Command B:

```bash
curl http://site2.local/
```

Means:

```text
Resolve site2.local to an IP address.
Connect to that IP.
Send Host: site2.local.
```

For browser access, Command B is closer to what the browser does.

Write:

```text
getent output:

curl http://site2.local/ status:

curl response content:

What this proves:

What this does not prove:
```

---

# Part 7 — Open in browser

Open:

```text
http://site2.local/
```

Also try:

```text
http://site1.local/
http://site3.local/
http://site4.local/
```

If `site1` is configured as the `app01` default, this may also work:

```text
http://app01/
```

Write:

```text
Browser URL:

Page shown:

Did it match curl?

What evidence supports my answer?
```

---

# Part 8 — Where Nginx fits

Name resolution gets the browser to the server.

Nginx still decides which site to serve.

Example request:

```http
GET / HTTP/1.1
Host: site2.local
```

Nginx compares:

```text
Host: site2.local
```

with:

```nginx
server_name site2.local;
```

Then Nginx chooses the matching server block.

## Stop and explain

Answer:

```text
What does /etc/hosts do?

What does the browser send as Host?

What does Nginx server_name do?

Does /etc/hosts choose the website root directory?

Does Nginx choose the website root directory?
```

Expected:

```text
/etc/hosts maps a name to an IP address.
The browser sends the hostname as the Host header.
Nginx uses server_name to choose the server block.
The Nginx server block chooses the root directory.
```

---

# Part 9 — Common misunderstanding

## Wrong

```text
DNS sends the website to my browser.
```

Better:

```text
DNS gives my computer the IP address.
Then my browser connects to that IP address.
Then HTTP asks for the website.
```

## Wrong

```text
The port chooses site2.
```

Better:

```text
Port 80 gets the request to Nginx.
The Host header helps Nginx choose site2.
```

## Wrong

```text
/etc/hosts changes the server.
```

Better:

```text
/etc/hosts changes how this local machine resolves names.
```

## Wrong

```text
DNS is exactly a global hosts file.
```

Better:

```text
DNS solves the same name-to-IP problem as hosts files, but it is distributed and scalable.
```

---

# Part 10 — Troubleshooting order

If `http://site2.local/` does not work in the browser, test in this order.

## 1. Does the name resolve?

```bash
getent hosts site2.local
```

If this fails, fix `/etc/hosts` or DNS.

## 2. Can curl reach the site by hostname?

```bash
curl -v http://site2.local/
```

If this works but the browser does not, suspect browser cache, proxy settings, or typo.

## 3. Can curl force the Host header?

```bash
curl -v -H 'Host: site2.local' http://app01/
```

If this works but `curl http://site2.local/` fails, the problem is probably name resolution.

## 4. Did Nginx see the request?

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.access.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/access.log'
```

## 5. Did Nginx report an error?

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.error.log'
```

Fallback:

```bash
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/error.log'
```

Write after each command:

```text
Question:

Evidence:

Conclusion:

Next narrow question:
```

---

# Final reflection

```text
Task:

Hostname tested:

IP address returned:

/etc/hosts entry used:

Command that proved name resolution:

Command that proved HTTP worked:

Browser URL:

Nginx access-log evidence:

What /etc/hosts does:

What DNS does:

How DNS is similar to /etc/hosts:

How DNS is different from /etc/hosts:

What Nginx server_name does:

One mistake I avoided:

One habit I practiced:
```

---

# Completion checkpoint

Explain without notes:

```text
A browser needs an IP address before it can connect.
A hostname must be resolved to an IP address.
/etc/hosts is a local name-to-IP file.
DNS is a distributed name-to-IP lookup system.
DNS is similar in purpose to /etc/hosts but is not one big global file.
http://site2.local/ uses port 80 by default.
The browser sends Host: site2.local.
Nginx uses server_name to select the site.
The Nginx server block decides the root or proxy behavior.
```
