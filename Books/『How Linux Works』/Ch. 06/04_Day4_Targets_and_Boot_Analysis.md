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

Do not overinterpret target names. A target is not a guarantee that every expected application is working.

## Lab 1: Inspect default target

```bash
systemctl get-default
systemctl list-units --type=target
systemctl status multi-user.target
systemctl status graphical.target
```

Questions:

1. What is the default target?
2. Is the machine server-style or graphical?
3. Which targets are active?
4. What does a target group together?

## Lab 2: Inspect dependencies

```bash
systemctl list-dependencies multi-user.target | less
systemctl list-dependencies graphical.target | less
```

Questions:

1. Which services are pulled in by `multi-user.target`?
2. Does SSH appear?
3. Does Nginx appear if enabled?
4. Why does dependency order matter during boot?

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

1. Why is rescue mode useful?
2. Why could switching targets break an SSH session?
3. Why should console access matter before rescue/emergency experiments?

## Lab 4: Boot timing

```bash
systemd-analyze
systemd-analyze blame | head -n 20
systemd-analyze critical-chain
```

Questions:

1. How long did userspace take?
2. Which services were slow?
3. Does `blame` automatically prove a service is a problem?
4. What does `critical-chain` show differently?

## Exit criterion

He can say:

```text
Targets organize boot and service dependencies. They are not the same thing as a working application.
```
