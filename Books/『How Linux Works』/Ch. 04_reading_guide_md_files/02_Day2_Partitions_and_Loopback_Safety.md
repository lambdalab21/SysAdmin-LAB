# Day 2 — Partitions, Loopback Files, and Safe Experimentation

## Read before lab

Read the Ch. 4 sections on viewing, modifying, and creating partition tables.

## Important warning

Do not practice partitioning on the Proxmox host or the VM’s main system disk.

Use one of these:

1. Extra disposable virtual disk, or
2. Loopback file created only for practice.

This guide uses a loopback file.

## Concept

A loop device lets a regular file act like a block device. This is useful for safe practice.

```text
regular file
→ loop device
→ partition table
→ partition
→ filesystem
→ mount point
```

## Lab 1: Create a fake disk file

```bash
mkdir -p ~/hlw-ch4
cd ~/hlw-ch4

fallocate -l 512M fake-disk.img
ls -lh fake-disk.img
```

Attach it as a loop device:

```bash
sudo losetup --find --show fake-disk.img
```

It should print something like:

```text
/dev/loop0
```

Save that path:

```bash
LOOPDEV=/dev/loop0
```

Replace `/dev/loop0` with the actual device printed on your machine.

Inspect:

```bash
lsblk "$LOOPDEV"
sudo fdisk -l "$LOOPDEV"
```

## Lab 2: Create a partition table

Use `fdisk` carefully on the loop device only:

```bash
sudo fdisk "$LOOPDEV"
```

Inside `fdisk`, create:

```text
g       create GPT partition table
n       new partition
Enter   default partition number
Enter   default first sector
+200M   size
w       write changes
```

Tell the kernel to re-read partitions:

```bash
sudo partprobe "$LOOPDEV" || true
lsblk "$LOOPDEV"
```

You may see a partition such as:

```text
/dev/loop0p1
```

If it does not appear, detach and reattach:

```bash
sudo losetup -d "$LOOPDEV"
sudo losetup --find --partscan --show fake-disk.img
```

Set `LOOPDEV` again if it changed.

## Lab 3: Explain what changed

Run:

```bash
lsblk -f "$LOOPDEV"
sudo fdisk -l "$LOOPDEV"
```

Answer:

1. Did creating a partition create a filesystem?
2. Is the partition mounted?
3. Does it have a filesystem type yet?
4. What is the difference between the loop device and its partition?

## Cleanup

Do not leave loop devices attached.

```bash
findmnt | grep loop || true
sudo losetup -a | grep fake-disk || true
sudo losetup -d "$LOOPDEV"
```

If mounted later, unmount before detaching.

## Retrieval questions

1. Why is a partition table not the same as a filesystem?
2. Why is `fdisk` dangerous on the wrong device?
3. What does `lsblk` show that `df -h` does not?
4. What does `df -h` show that `lsblk` does not?
5. Why is loopback practice safer than touching a real disk?
