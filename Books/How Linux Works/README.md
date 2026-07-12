 #Book/How-Linux-Works 
 #README 
## Recommended combined order

| Order | **How Linux Works**                                | Pair with **The Linux Command Line**                | What he should learn                                                           |
| ----- | -------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------ |
| 1     | **Ch. 1 — The Big Picture**                        | LCL Ch. 1                                           | Shell vs kernel vs user space; what Linux is.                                  |
| 2     | **Ch. 2 — Basic Commands and Directory Hierarchy** | LCL Ch. 2–5                                         | Navigation, files, directories, command lookup, man pages.                     |
| 3     | **Ch. 7 — System Configuration**                   | LCL Ch. 9, 11, maybe 13                             | Users, groups, permissions, sudo, environment, startup files, cron/jobs, logs. |
| 4     | **Ch. 8 — Processes and Resource Utilization**     | LCL Ch. 10                                          | Processes, signals, `ps`, `top`, `kill`, jobs, resource usage.                 |
| 5     | **Ch. 9 — Networking**                             | LCL networking chapter, if present in his edition   | IP addresses, routing, DNS, ports, basic troubleshooting.                      |
| 6     | **Ch. 10 — Network Applications and Services**     | LCL package/networking-related chapters, if present | SSH, servers, clients, `curl`, `ss`, service debugging.                        |
| 7     | **Ch. 11 — Shell Scripts**                         | LCL Ch. 6–7, then scripting chapters                | Redirection, pipes, quoting, variables, conditionals, loops.                   |
| 8     | **Ch. 4 — Disks and Filesystems**                  | LCL storage media / filesystems chapter             | Mounting, partitions, filesystems, `/etc/fstab`, disk usage.                   |
| 9     | **Ch. 6 — How User Space Starts**                  | LCL mostly no direct match                          | systemd, units, boot targets, service startup.                                 |
| 10    | **Ch. 17 — Virtualization**                        | LCL no strong match                                 | VMs, containers, Docker/Podman concepts.                                       |
## Important adjustment

Do **not** wait until Ward Ch. 11 to read LCL redirection and quoting.

Even though LCL Ch. 6–7 pair naturally with shell scripting, he should read them **early**, because redirection, pipes, expansion, and quoting are used everywhere.

So the real order should be:

1. Ward 1 + LCL 1
2. Ward 2 + LCL 2–5
3. **LCL 6–7 early**
4. Ward 7 + LCL 9, 11
5. Ward 8 + LCL 10
6. Ward 9–10 + LCL networking/package chapters
7. Ward 11 + LCL scripting chapters
8. Ward 4 + LCL storage chapter
9. Ward 6
10. Ward 17