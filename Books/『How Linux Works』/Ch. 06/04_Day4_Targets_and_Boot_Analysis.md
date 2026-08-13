# Day 4 — Targets and Boot Analysis

## Read before lab

Read the Ch. 6 sections on systemd targets and boot states.

## Concept

A target is a grouping or synchronization point.

| Target | Rough meaning |
|---|---|
| `multi-user.target` | multi-user non-graphical system |
| `graphical.target` | graphical system |
| `rescue.target` | basic rescue shell with limited services |
| `emergency.target` | minimal emergency shell |
| `network.target` | basic network state reached |

## Lab 1: Inspect default target

```bash
systemctl get-default
systemctl list-units --type=target
systemctl status multi-user.target
systemctl status graphical.target
```

Questions:

1. What is the default target? `graphical.target` and/or `multi-user.target`
2. Is the machine server-style or graphical? Graphical if default is `graphical.target`; server-style if multi-user.target.
3. Which targets are active? The ones currently reached. 
4. What does a target group together? A set of units that should be active together. 

## Lab 2: Inspect dependencies

```bash
systemctl list-dependencies multi-user.target | less
systemctl list-dependencies graphical.target | less
```

Questions:

1. Which services are pulled in by `multi-user.target`? Core multi-user services such as `systemd-logind, systemd-user-sessions, getty`, networking, and any enabled multi-user units. 
2. Does SSH appear? Yes, if the SSH service is enabled and wanted by multi-user. 
3. Does Nginx appear if enabled? Yes, if Nginx is enabled and pulled in by the target. 
4. Why does dependency order matter during boot? Services must start after their required dependencies are ready; otherwise boot can fall or race. 

## Lab 3: Rescue mode concept, not reckless practice

Do **not** casually run this over SSH:

```bash
sudo systemctl isolate rescue.target
```

It can disrupt the session.

Instead inspect:

```bash
systemctl cat rescue.target
systemctl list-dependencies rescue.target
systemctl cat emergency.target
```

Questions:

1. Why is rescue mode useful? It provides a limited shell and basic services for repairing a broken system. 
2. Why could switching targets break an SSH session? Isolating a different target stops many services the ssh session depends on, dropping the connection. 
3. Why should console access matter before rescue/emergency experiments? 

## Lab 4: Boot timing

```bash
systemd-analyze
systemd-analyze blame | head -n 20
systemd-analyze critical-chain
```

Questions:

1. How long did userspace take? The time reportedly by systemd-analyze for usernames. 
2. Which services were slow? The longest ones listed near the top of systemd-analyze blame. 
3. Does `blame` automatically prove a service is a problem? No. 
4. What does `critical-chain` show differently? The sequence of units that delayed the final target the most. 