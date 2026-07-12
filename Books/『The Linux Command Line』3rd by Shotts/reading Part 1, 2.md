#Book/The-Linux-Command-Line #Author/Shotts #README 
Yes, mostly. Keep **_How Linux Works_** as the spine, then attach the matching **_The Linux Command Line_** chapters where they help.

Correction: _Linux_, not “linx.”

## Recommended combined order

| Order | **How Linux Works**                                | Pair with **The Linux Command Line**                                 | What he should learn                                                           |
| ----: | -------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
|     1 | **Ch. 1 — The Big Picture**                        | LCL Ch. 1                                                            | Shell vs kernel vs user space; what Linux is.                                  |
|     2 | **Ch. 2 — Basic Commands and Directory Hierarchy** | LCL Ch. 2–5                                                          | Navigation, files, directories, command lookup, man pages.                     |
|     3 | **Ch. 7 — System Configuration**                   | LCL Ch. 9, 11, maybe 13                                              | Users, groups, permissions, sudo, environment, startup files, cron/jobs, logs. |
|     4 | **Ch. 8 — Processes and Resource Utilization**     | LCL Ch. 10, Ch 17                                                    | Processes, signals, `ps`, `top`, `kill`, jobs, resource usage.                 |
|     5 | **Ch. 9 — Networking**                             | LCL networking chapter, if present in his edition                    | IP addresses, routing, DNS, ports, basic troubleshooting.                      |
|     6 | **Ch. 10 — Network Applications and Services**     | LCL package/networking-related chapters, if present                  | SSH, servers, clients, `curl`, `ss`, service debugging.                        |
|     7 | **Ch. 11 — Shell Scripts**                         | LCL Ch. 6–7, then scripting chapters 21-34, then Author/Shotts' Ch 11.      | Redirection, pipes, quoting, variables, conditionals, loops.                   |
|     8 | **Ch. 4 — Disks and Filesystems**                  | LCL storage media / filesystems chapter Author/Shotts Ch 15: Storage Media" | Mounting, partitions, filesystems, `/etc/fstab`, disk usage.                   |
|     9 | **Ch. 6 — How User Space Starts**                  | LCL mostly no direct match                                           | systemd, units, boot targets, service startup.                                 |
|    10 | **Ch. 17 — Virtualization**                        | LCL no strong match                                                  | VMs, containers, Docker/Podman concepts.                                       |

## Important adjustment

Do **not** wait until Ward Ch. 11 to read LCL redirection and quoting.

Even though LCL Ch. 6–7 pair naturally with shell scripting, he should read them **early**, because redirection, pipes, expansion, and quoting are used everywhere.

So the real order should be:

1. Ward 1 + LCL 1
2. **LCL 6–7 early**
3. Ward 7 + LCL 9, 11
4. Ward 8 + LCL 10
5. Ward 9–10 + LCL networking/package chapters
6. Ward 11 + LCL scripting chapters
7. Ward 4 + LCL storage chapter
8. Ward 6
9. Ward 17

## Why this works

Ward tells him **what Linux is doing**.

LCL gives him **the hands-on command-line muscle**.

So yes, map LCL chapters to the Ward order, but with one exception: **move LCL redirection and quoting near the beginning**. Without those, he will misuse the shell for months.

## Minimal no-nonsense plan

If you want the simplest version:

### Phase 1: Basic shell and filesystem

- Ward 1–2
- LCL 1–7

### Phase 2: Users, permissions, processes

- Ward 7–8
- LCL 9–11

### Phase 3: Networking and services

- Ward 9–10
- LCL networking/package chapters if included

### Phase 4: Scripting and automation

- Ward 11
- LCL scripting chapters

### Phase 5: Storage, boot, containers

- Ward 4, 6, 17
- LCL storage chapter if included

That is the cleanest pairing.