# Day 5 — Swap

## Read before lab

Read the Ch. 4 section on swap space.

## Goal

Understand that swap is storage used as backing space for memory pages. It is not a normal filesystem.

## Safety

Use a small swap file in a disposable VM. Do not make huge swap files.

## Lab 1: Inspect current swap

```bash
free -h
swapon --show
cat /proc/swaps
```

Answer:

1. Does the system currently use swap?
2. Is swap a partition or file?
3. How large is it?

## Lab 2: Create a temporary swap file

```bash
sudo fallocate -l 256M /swapfile-hlw-test
sudo chmod 600 /swapfile-hlw-test
sudo mkswap /swapfile-hlw-test
sudo swapon /swapfile-hlw-test
```

Inspect:

```bash
swapon --show
free -h
cat /proc/swaps
```

## Lab 3: Turn it off

```bash
sudo swapoff /swapfile-hlw-test
swapon --show
sudo rm /swapfile-hlw-test
```

## Questions

1. Why must the swap file have restrictive permissions?
2. What does `mkswap` do?
3. What does `swapon` do?
4. What does `swapoff` do?
5. Why is swap slower than RAM?
6. Why is swap not a replacement for enough memory?
7. How is swap different from a mounted filesystem?

## Stretch question

If a system is slow and `free -h` shows swap in use, is swap definitely the cause?

Good answer:

```text
Not automatically. Swap use can be normal, but heavy swap-in/swap-out activity during workload often indicates memory pressure. Use vmstat si/so columns to observe active swapping.
```
