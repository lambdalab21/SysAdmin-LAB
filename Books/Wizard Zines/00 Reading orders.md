#Book/Wizard-Zine

The box set **_Your Linux Toolbox_** includes seven zines: _So You Want to Be a Wizard_, _Bite-Size Linux!_, _Networking, ACK!_, _Let’s Learn tcpdump!_, _Linux Debugging Tools You’ll Love_, _Spying on Your Programs with strace_, and _Profiling & Tracing with perf_. ([nostarch.com](https://nostarch.com/linuxtoolbox?utm_source=chatgpt.com "Your Linux Toolbox"))

Wizard Zines currently lists additional titles such as _Bite Size Networking!_, _Bite Size Bash!_, _The Secret Rules of the Terminal_, _How Git Works_, _How DNS Works_, _HTTP_, _How Containers Work!_, and others. ([wizard zines](https://wizardzines.com/?utm_source=chatgpt.com "wizard zines"))

## Recommended order for him

### Stage 1: General Linux confidence

Read first:

1. **_So You Want to Be a Wizard_**
    
2. **_Bite-Size Linux!_**
    

These are motivational and confidence-building. They help him see that Linux is not magic; it is inspectable. This should pair with Ward Ch. 1–2 and Author/Shotts early chapters.

Do not spend too long here. Read, then use the shell.

---

### Stage 2: Processes and debugging — Ward Ch. 8

Read:

3. **_Linux Debugging Tools You’ll Love_**
    
4. **_Spying on Your Programs with strace_**
    

These should come while reviewing Ward Ch. 8. They reinforce the idea that you can inspect what programs are doing instead of guessing.

Pair with:

```bash
ps
top
lsof
strace
journalctl
systemctl status
```

Do **not** read _Profiling & Tracing with perf_ yet. `perf` is useful, but it is too deep for the current stage.

---

### Stage 3: Networking — Ward Ch. 9, Author/Shotts Ch. 16, Lucas

Read:

5. **_Networking, ACK!_**
    
6. **_Bite Size Networking!_**
    

This is the most important pair for the next phase.

Use _Networking, ACK!_ for the big picture. Use _Bite Size Networking!_ for practical command familiarity. The _Bite Size Networking!_ page specifically mentions tools such as `ping`, `curl`, `dig`, `ssh`, `scp`, `rsync`, `nc`, `ss`, `iptables`, and `nftables`, which match his current path well. ([wizard zines](https://wizardzines.com/?utm_source=chatgpt.com "wizard zines"))

Pair with:

```bash
ip addr
ip route
ping
traceroute
dig
curl -v
ss -tulpn
scp
rsync
nc
```

---

### Stage 4: DNS and HTTP — Nginx static site phase

Read:

7. **_How DNS Works_**
    
8. **_HTTP: Learn Your Browser’s Language!_**
    

Read these when he is serving `site1.local` from Nginx.

DNS matters when he uses `/etc/hosts`, hostnames, `dig`, and `getent hosts`.

HTTP matters when he uses:

```bash
curl -v http://app01
curl -I http://app01
curl -H "Host: site1.local" http://app01
```

This is where he learns that “the website does not work” can mean different failures:

```text
hostname resolution failed
TCP connection failed
firewall blocked port 80
Nginx returned 403
Nginx returned 404
Nginx returned 500
wrong Host header
wrong document root
```

---

### Stage 5: Bash automation — deploy scripts

Read:

9. **_Bite Size Bash!_**
    
10. **_The Secret Rules of the Terminal_**
    

This should happen when he writes:

```bash
deploy.sh
check-site.sh
backup-site.sh
```

_Bite Size Bash!_ reinforces practical shell scripting. _The Secret Rules of the Terminal_ helps explain terminal behavior, foreground/background jobs, Ctrl-C, Ctrl-Z, shells, and confusing command-line interactions.

This pairs with Ward Ch. 11 and Author/Shotts shell chapters.

---

### Stage 6: Git workflow

Read:

11. **_How Git Works_**
    

Read this when he starts using Git for `site1`.

Do not start with GitHub Actions. First:

```bash
git init
git status
git add
git commit
git log
git diff
```

Then later:

```bash
git remote
git push
git pull
```

The zine should support a real workflow:

```text
edit site
→ test locally
→ commit
→ build or prepare public/
→ rsync deploy
```

---

### Stage 7: Troubleshooting mindset

Read:

12. **_The Pocket Guide to Debugging_**
    

Read this after he has had a few failures:

- permission denied
    
- Nginx 403
    
- Nginx 404
    
- SSH works but `scp` fails
    
- firewall blocks HTTP
    
- wrong `/etc/hosts`
    
- occupied port
    

It will be more valuable after he has pain. Debugging advice before pain becomes vague.

---

### Stage 8: Later, after systemd backend and database

Read:

13. **_Become a SELECT Star!_**
    

Read this if he connects `app01` to `db01` and uses SQL seriously.

Later still:

14. **_How Containers Work!_**
    

Do not read this now. Read it when he has already deployed a backend app without containers and can explain why containers solve a real packaging/isolation problem.

---

### Stage 9: Optional / low priority

Read later or skip for now:

15. **_Profiling & Tracing with perf_**
    

Useful, but later. He should first know `ps`, `top`, `lsof`, `strace`, `vmstat`, and basic logs.

16. **_Bite Size Command Line!_**
    

Probably optional because he is already reading Author/Shotts and _Bite-Size Linux!_. Buy/use it only if Author/Shotts feels too dry.

17. **_Oh Shit, Git!_**
    

Use later as a Git rescue guide. Read _How Git Works_ first.

18. **_Hell Yes! CSS!_**
    

Good if he wants the static sites to look better, but not important for infrastructure.

19. **_Help! I Have a Manager!_**
    

Not relevant for his current stage.

## Best order, compressed

For the next few months:

```text
1. So You Want to Be a Wizard
2. Bite-Size Linux!
3. Linux Debugging Tools You’ll Love
4. Spying on Your Programs with strace
5. Networking, ACK!
6. Bite Size Networking!
7. How DNS Works
8. HTTP: Learn Your Browser’s Language!
9. Bite Size Bash!
10. The Secret Rules of the Terminal
11. How Git Works
12. The Pocket Guide to Debugging
```

Later:

```text
13. Become a SELECT Star!
14. How Containers Work!
15. Profiling & Tracing with perf
```

## How to use them

Do not let him “read zines” as another consumption habit.

Use this rule:

> One zine → one experiment → one explanation.

Example:

After _Networking, ACK!_:

```bash
ip addr
ip route
ping -c 3 app01
curl -v http://app01
```

After _Spying on Your Programs with strace_:

```bash
strace -e trace=openat cat /etc/hostname
strace -e trace=openat cat /tmp/missing-file
```

After _HTTP_:

```bash
curl -v http://app01
curl -I http://app01
curl -H "Host: site1.local" http://app01
```

The zines are excellent reinforcement. They should not replace Ward, Author/Shotts, Lucas, or the labs. They should make the hard ideas stick.