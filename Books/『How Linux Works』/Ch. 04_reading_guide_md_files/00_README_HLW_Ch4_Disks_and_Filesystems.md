# How Linux Works Ch. 4 — Disks and Filesystems Reading Guide

## Purpose

This guide turns Chapter 4 into safe, disciplined infrastructure practice.

The student should not merely learn commands such as `lsblk`, `mount`, `mkfs`, and `fsck`. He should understand the storage stack:

```text
physical or virtual disk
→ partition table
→ partition
→ filesystem
→ mount point
→ ordinary directory tree
→ user process reads/writes files
```

## Safety rule

Do **not** run destructive storage commands on the Proxmox host or a machine with important data.

Dangerous commands include:

```bash
fdisk
parted
mkfs
wipefs
dd
fsck
mkswap
swapon
```

They are not evil. They are powerful. Use them only on a disposable VM or a deliberately created loopback file.

## Recommended lab environment

Use one disposable Linux VM.

Best option:

```text
practice VM
extra virtual disk, or loopback file
no important data
snapshotted before lab
```

If using Proxmox, attach a small extra virtual disk such as 2–5 GB. If that is not convenient, use the loopback-file labs in these notes.

## Tools to install

On AlmaLinux:

```bash
sudo dnf install -y util-linux e2fsprogs xfsprogs lvm2
```

On Ubuntu:

```bash
sudo apt update
sudo apt install -y util-linux e2fsprogs xfsprogs lvm2
```

## Core commands to recognize

```bash
lsblk
blkid
findmnt
mount
umount
df -h
du -sh
fdisk -l
parted
mkfs.ext4
mkfs.xfs
fsck
mkswap
swapon
swapoff
losetup
fallocate
```

## Study method

For every reading session:

1. Read the assigned section.
2. Predict what each command should show.
3. Run the lab on a safe target.
4. Explain the storage layer being inspected.
5. Write down how to undo the change.

## Final target

By the end, he should be able to explain:

```text
A disk is not automatically a filesystem.
A partition is not automatically mounted.
A mounted filesystem appears as part of the directory tree.
Filesystems have type, UUID, labels, metadata, and repair tools.
Swap is not a normal filesystem.
LVM adds a layer between physical storage and filesystems.
```
