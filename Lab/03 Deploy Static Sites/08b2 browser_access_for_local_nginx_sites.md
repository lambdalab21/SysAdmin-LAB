# Follow-Up: How to Open `site1`, `site2`, and `site3` in a Browser

## Purpose

In the deployment labs, you tested sites with `curl`.

For example:

```bash
curl http://app01/
curl -H 'Host: site2.local' http://app01/
curl -H 'Host: site3.local' http://app01/
```

This file explains how to open those same sites in a normal browser.

The key idea:

```text
curl can manually set the Host header.
A normal browser usually cannot.
So the browser must visit the hostname directly.
```

---

# Big picture

A browser request has three separate parts:

```text
1. Which machine should I connect to?
2. Which port should I use?
3. Which website name am I asking Nginx for?
```

Example:

```text
http://site2.local/
```

means:

```text
Connect to the IP address for site2.local.
Use HTTP default port 80.
Send Host: site2.local.
Ask for path /.
```

---

# Feynman version

Explain it to a younger student:

```text
The IP address gets you to the building.
The port gets you to the correct door.
The Host header tells Nginx which room inside the building you want.
```

Example:

```text
app01 = building
port 80 = front door
site2.local = room name
```

---

# Part 1 — Why does `http://app01/` not show every site?

When you type:

```text
http://app01/
```

the browser sends a request like:

```http
GET / HTTP/1.1
Host: app01
```

Nginx sees:

```text
Host: app01
```

Then Nginx chooses the matching `server_name`, or the default server if there is no exact match.

So `http://app01/` usually opens the default site or the site configured for:

```nginx
server_name app01;
```

It does not automatically show `site2.local` or `site3.local`.

---

# Part 2 — Where is the port number?

This:

```text
http://app01/
```

really means:

```text
http://app01:80/
```

HTTP uses port 80 by default.

These are equivalent:

```text
http://app01/
http://app01:80/
```

For HTTPS, the default port is 443:

```text
https://example.com/
https://example.com:443/
```

## Stop and think

Answer before continuing:

```text
What port does HTTP use by default?

What port does HTTPS use by default?

When I type http://app01/, what port is used?

Does the port decide which site Nginx serves?
```

Expected:

```text
HTTP uses 80.
HTTPS uses 443.
http://app01/ uses port 80.
The port gets the request to Nginx, but the Host header helps Nginx choose the site.
```

---

# Part 3 — Why `curl -H 'Host: site2.local' http://app01/` works

This command:

```bash
curl -H 'Host: site2.local' http://app01/
```

means:

```text
Connect to app01 on port 80.
But send Host: site2.local.
```

So Nginx receives a request like:

```http
GET / HTTP/1.1
Host: site2.local
```

Then Nginx can match:

```nginx
server {
    listen 80;
    server_name site2.local;

    root /var/www/site2/public;
}
```

## What this proves

```text
curl can connect to app01.
Nginx can select site2 by Host header.
Nginx can serve site2 content.
```

## What this does not prove

```text
It does not prove the browser can open http://site2.local/ yet.
The browser machine still needs to know what IP address site2.local means.
```

---

# Part 4 — How to open `site2.local` in a browser

A browser should open:

```text
http://site2.local/
```

But first, your browser machine must know that:

```text
site2.local points to app01's IP address
```

That is usually done with `/etc/hosts` in this home lab.

---

# Part 5 — Find `app01`'s IP address

From the machine where you normally run terminal commands:

```bash
getent hosts app01
```

Example output:

```text
192.168.0.107 app01
```

If that does not work, SSH into `app01` and run:

```bash
ip addr
```

Look for the LAN IP address, probably something like:

```text
192.168.x.x
```

Write:

```text
app01 IP address:

Command used to find it:

What this proves:
```

---

# Part 6 — Edit `/etc/hosts` on the browser machine

On the computer where the browser runs, edit:

```bash
sudo vi /etc/hosts
```

Add one line like this, using your real `app01` IP:

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

Either style is fine.

## Stop and think

Before saving, answer:

```text
Which machine's /etc/hosts am I editing?

Is it app01, or the computer running the browser?

What IP address am I mapping the names to?

What names am I adding?
```

Expected:

```text
Edit /etc/hosts on the browser machine.
Map site2.local to app01's IP address.
Then the browser can connect to app01 while sending Host: site2.local.
```

Green check:

```text
[ ] I edited the browser machine's /etc/hosts.
[ ] I used app01's IP address.
[ ] I added site2.local.
[ ] I did not edit a random server file without knowing why.
```

---

# Part 7 — Verify name resolution before using the browser

Run:

```bash
getent hosts site2.local
```

Expected example:

```text
192.168.0.107 site2.local
```

Also test with curl without manually setting the Host header:

```bash
curl -v http://site2.local/
```

This should now send:

```http
Host: site2.local
```

Write:

```text
getent result:

curl command:

HTTP status:

Response content:

What this proves:
```

Expected:

```text
The browser machine can resolve site2.local to app01's IP.
curl can connect using the site2.local name.
Nginx sees Host: site2.local.
```

---

# Part 8 — Open in the browser

Now open:

```text
http://site2.local/
```

For other sites:

```text
http://site1.local/
http://site2.local/
http://site3.local/
http://site4.local/
```

If `site1` is configured as `app01`, you may also open:

```text
http://app01/
```

Write:

```text
Browser URL:

What page appeared:

Did it match curl?

What evidence supports my answer?
```

---

# Part 9 — Check Nginx logs after browser access

On `app01`, check:

```bash
sudo tail -n 20 /var/log/nginx/site2.access.log
```

Fallback:

```bash
sudo tail -n 20 /var/log/nginx/access.log
```

Look for a request that includes:

```text
GET /
```

and status:

```text
200
```

Write:

```text
Access-log line:

Status code:

What this proves:

What this does not prove:
```

Expected:

```text
The access log proves the browser request reached Nginx.
It does not prove the page is visually correct in every browser.
```

---

# Part 10 — Common mistakes

## Mistake 1: Editing `/etc/hosts` on the wrong machine

If the browser is on your laptop, edit `/etc/hosts` on your laptop.

Do not only edit `/etc/hosts` on `app01` unless the browser is running on `app01`.

## Mistake 2: Forgetting the Host header idea

This:

```bash
curl -H 'Host: site2.local' http://app01/
```

is not the same as typing:

```text
http://app01/
```

in the browser.

The browser version should be:

```text
http://site2.local/
```

## Mistake 3: Thinking the port chooses the site

Port 80 gets the request to Nginx.

The hostname / Host header helps Nginx choose the site.

## Mistake 4: Browser cache confusion

If the browser shows an old page, test with curl:

```bash
curl -v http://site2.local/
```

Then refresh the browser with cache bypass:

```text
Ctrl+F5
```

or open a private window.

## Mistake 5: `.local` name conflicts

Some systems use `.local` for mDNS.

If `site2.local` behaves strangely, use a different lab name, such as:

```text
site2.test
site3.test
site4.test
```

Then update both `/etc/hosts` and Nginx `server_name`.

---

# Troubleshooting order

If the browser does not work, use this order:

```bash
getent hosts site2.local
curl -v http://site2.local/
curl -v -H 'Host: site2.local' http://app01/
ssh deploy@app01 'find /var/www/site2/public -type f -print | sort'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.access.log'
ssh your-admin-user@app01 'sudo tail -n 20 /var/log/nginx/site2.error.log'
```

For each command, write:

```text
Question:

Evidence:

Conclusion:

Next narrow question:
```

---

# Final reflection

```text
Site opened:

Browser URL:

app01 IP address:

/etc/hosts entry:

Command that proved name resolution:

Command that proved HTTP worked:

Access-log evidence:

What the port did:

What the Host header did:

One mistake I avoided:

One habit I practiced:
```

---

# Completion checkpoint

Explain without notes:

```text
http://app01/ means app01 on port 80.
http://site2.local/ means site2.local on port 80.
The browser sends Host: site2.local when the URL uses site2.local.
Nginx uses server_name to choose the site.
curl -H can fake the Host header manually.
A normal browser should use the hostname directly.
The browser machine needs /etc/hosts or DNS to know where site2.local points.
The Nginx access log proves the browser request reached Nginx.
```
