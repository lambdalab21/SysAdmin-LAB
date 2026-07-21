#Author/Lucas #ssh #SSH-Mastery #network 
# SSH Mastery Reading Guide — Chapter 3: The OpenSSH Server

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- What `sshd` is
- Where server configuration usually lives
- How to check service status
- How server-side policy affects login
- Safe editing habits for `sshd_config`

### Skim

- Rare server options
- Platform-specific package details not used by Ubuntu/AlmaLinux
- Advanced hardening options until he understands the basic login path

### Skip for now

- Do not make production-hardening changes just because they sound secure. Do not change port, firewall, and authentication method in the same session.

## Important ideas to hunt for

Do not read mindlessly. Look for answers to these:

- What process accepts SSH connections? sshd
- What port does it usually listen on? 22
- Where is the main server config file? /etc/ssh/sshd_config
- What logs show failed login attempts? /var/log/auth.log or journalctl
- Why can server config lock out a real user? server config can lock out users via restrictive permitrootlogin, passwordauthentication, etc. 

## Stop-and-think questions

After each major section, answer at least one:

- What is the difference between `ssh` and `sshd`? ssh is the client, sshd is the server daemon. 
- Why keep a second session open before changing `sshd_config`? Keep a second session open as a safety net if you break the config. 
- Why test config before restarting the service? sshd -t validates syntax before reload to avoid lockout. 
- What does `ss -ltnp` prove about SSH? ss -ltmp proves sshd is listening on port 22. 

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- On Alma/RHEL-like systems: `sudo systemctl status sshd`.
- On Ubuntu/Debian: `sudo systemctl status ssh`.
- Run `sudo ss -ltnp | grep ':22'`.
- Run `sudo sshd -t` where available before any server config reload.

Use this format:

```text
Question I am testing: Is SSH listening? 

Command or file inspected: sudo ss -ltmp | grep ':22'

Expected evidence: sshd on tcp port 22. 

Actual evidence: Matches expectation. 

Conclusion: Service is running and reachable locally. 

What this does NOT prove: Remote access or auth success. 
```
