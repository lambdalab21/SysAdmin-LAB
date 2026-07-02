# Ward Ch. 10 Troubleshooting Drills

These drills force evidence-based thinking. Do not guess. Use commands to prove each layer.

## The troubleshooting ladder

When a network service fails, check in this order:

```text
1. Did I use the right hostname/IP?
2. Does the name resolve?
3. Is the machine reachable?
4. Is the route reasonable?
5. Is the service listening?
6. Is the firewall allowing it?
7. Does tcpdump show packets arriving?
8. Do logs show an application/authentication error?
9. Are credentials/permissions correct?
10. Is the client using the right protocol?
```

## Drill 1: SSH fails

Symptom:

```text
ssh john@app01 fails
```

Use:

```bash
getent hosts app01
ping -c 3 app01
nc -vz app01 22
ssh -v john@app01
```

On app01:

```bash
systemctl status sshd
ss -tulpen | grep ':22'
sudo journalctl -u sshd -n 50
sudo firewall-cmd --list-all
```

Write:

```text
Problem category:
Evidence:
Fix:
Prevention:
```

## Drill 2: db01 is reachable but MySQL is not

Symptom:

```text
ping db01 works, but app cannot connect to MySQL/MariaDB.
```

From app01:

```bash
getent hosts db01
ping -c 3 db01
nc -vz db01 3306
```

On db01:

```bash
systemctl status mariadb
ss -tulpen | grep ':3306'
sudo lsof -iTCP:3306 -sTCP:LISTEN -P -n
sudo ufw status verbose
sudo journalctl -u mariadb -n 50
```

Questions:

```text
Is MariaDB running?
Is it listening?
Which address is it bound to?
Is the firewall allowing app01?
Did the connection reach db01?
```

## Drill 3: Service is listening but login fails

Symptom:

```text
nc says port 22 or 3306 is reachable, but login fails.
```

Explain:

```text
Port reachability is not the same as authentication success.
```

SSH checks:

```bash
ssh -v john@app01
sudo journalctl -u sshd -n 50   # AlmaLinux
sudo journalctl -u ssh -n 50    # Ubuntu
```

Database checks:

```bash
mysql -h db01 -u crud_user -p crudapp
sudo journalctl -u mariadb -n 50
```

Write:

```text
Network layer result:
Authentication result:
Authorization result:
```

## Drill 4: Find who owns a port

Given a port number, identify the process.

```bash
sudo ss -tulpen
sudo lsof -i -P -n
```

Questions:

```text
Which program owns the port?
What PID?
What user?
What systemd service might control it?
```

Then:

```bash
ps -fp <PID>
systemctl status <service>
```

## Drill 5: tcpdump truth test

Run on db01:

```bash
sudo tcpdump -i any host app01 and port 3306
```

From app01:

```bash
nc -vz db01 3306
```

Interpret:

```text
tcpdump shows nothing:
  traffic may not be reaching db01; check hostname, route, firewall before host, wrong IP.

tcpdump shows SYN but no useful response:
  db01 saw the attempt; check local firewall/service state.

tcpdump shows connection activity:
  network path exists; check service logs and credentials.
```

## Drill 6: Open port audit

On each server:

```bash
sudo ss -tulpen
sudo lsof -iTCP -sTCP:LISTEN -P -n
```

From the other server:

```bash
nmap app01
nmap db01
```

Create this table:

```text
Port | Service | Expected? | Who should access it? | Keep/close? | Evidence
```

Do not close anything until you can explain what it is.

## Drill 7: The bad explanation detector

Rewrite each bad sentence into a professional sentence.

Bad:

```text
The server is broken.
```

Better:

```text
The client can resolve app01 and ping it, but TCP connection to port 22 times out. On app01, sshd is running and listening, so I suspect firewall filtering between the client and sshd.
```

Bad:

```text
MySQL does not work.
```

Better:

```text
MariaDB is running on db01, but ss shows it is bound only to 127.0.0.1, so app01 cannot connect over the LAN.
```

Bad:

```text
SSH password is wrong.
```

Better:

```text
The client reaches sshd, and server logs show failed password authentication for user john. Network reachability is fine; authentication failed.
```

## Final drill: Diagnose without touching config first

Rule:

> Observe before changing.

Before editing any file or restarting any service, run:

```bash
hostname
ip addr
ip route
ss -tulpen
systemctl status <service>
journalctl -u <service> -n 50
sudo lsof -i -P -n
```

Then write the diagnosis. Only then change configuration.
