#Author/Lucas #Book/Network-for-System-Administrators #network 
# Chapter 4 — IPv6

**Book:** Michael W. Lucas, *Networking for System Administrators*, 2nd edition  
**Purpose of this guide:** prevent passive reading.

## Before reading

Answer these before opening the chapter.

- Why did IPv4 address shortage become a problem? IPv4 address shortage became a problem because there are only ~4.3 billion addresses(2^32), exhausted by internet growth, mobile devices, and IoT. 
- Does your machine currently have IPv6 addresses? Yes
- Why is ignoring IPv6 dangerous during troubleshooting? Because many networks are dual-stack; issues can appear only on IPv6 paths, breaking connectivity or making problems during troubleshooting. 

## After reading

Do these before moving on.

- Run `ip -6 addr` and `ip -6 route`. 
- Identify loopback, link-local, and global IPv6 addresses if present.
- Write what you can recognize now and what you cannot configure confidently yet.

## Stop-and-think questions

1. What problem does this chapter help diagnose? Diagnoses IPv6-related connectivity failures hidden in hual-stack environments. 
2. What evidence would prove the chapter’s main idea on a real Linux machine? Seeing global IPv6 addresses, successful pingo to external hsots, or IPv6 routes. 
3. What would a lazy beginner wrongly assume here? A lazy beginner assumes "IPv4 is enough" and ignores IPv6 entirely. 
4. How does this apply to `client01 -> app01 -> db01`? In client01 -> app01 -> db01, ensures all hops support IPv6 for full connectivity; IPv6 can bypass IPv4 NAT issues. 