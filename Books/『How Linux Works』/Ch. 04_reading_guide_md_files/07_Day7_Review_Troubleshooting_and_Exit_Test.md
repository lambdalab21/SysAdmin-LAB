# Day 7 — Review, Troubleshooting, and Exit Test

## Challenge 1: Why does `df` not show my new filesystem?

Setup:

Create a fake disk, partition it, and run `mkfs`, but do not mount it.

Ask:

```text
Why does `df -h` not show the new filesystem? 'df' shows mounted filesystems. A filesystem can exist on a device, but not appear in 'df' until it's mounted.
```

Useful tools:

```bash
lsblk -f
blkid
findmnt
df -h
```

## Challenge 2: Why can’t I unmount?

Setup:

Mount a loopback filesystem and `cd` into it in one terminal.

In another terminal:

```bash
sudo umount /mnt/hlw-ch4
```

Expected explanation:

```text
A process has its current directory or an open file inside the mounted filesystem. The target is busy.
```

Useful tools:

```bash
lsof +f -- /mnt/hlw-ch4
fuser -vm /mnt/hlw-ch4
```

## Challenge 3: Why is writing denied?

Mount read-only:

```bash
sudo mount -o ro "$PART" /mnt/hlw-ch4
```

Try to write.

Expected explanation:

```text
Filesystem permissions are not the only gate. Mount options can make the entire filesystem read-only.
```

Useful tools:

```bash
findmnt -o TARGET,SOURCE,FSTYPE,OPTIONS /mnt/hlw-ch4
mount | grep hlw-ch4
```

## Challenge 4: Why is the web server using the wrong files?

Create an empty directory `/mnt/hlw-ch4`, place one file there, then mount another filesystem over it.

Question:

```text
Why did the original file disappear from view?
```

Expected explanation:

```text
The mounted filesystem hides the previous contents of the mount point while mounted. The old files are not necessarily deleted; they are obscured.
```

## Exit questions

Answer without notes:

1. What is the difference between a disk and a partition? A disk is the entire physical/virtual storage device. A partition is completely different, acting as a logical subdivision of the disk. 
2. What is the difference between a partition and a filesystem? A partition is a slice of a disk and a filesystem is the structured format placed on a partition for storing files.
3. What does `mkfs` do? mkfs creates a filesystem on a partition or a device. 
4. What does `mount` do? mount attaches a filesystem to a directory in the directory tree, making it accessible. 
5. What does `umount` do? umount detaches a mounted filesystem from the directory tree. 
6. What does `df -h` show? df -h shows disk space usage of mounted filesystems in human-readable format. 
7. What does `du -sh` show? du -sh shows the total disk usage of files and directores in a human-readable format. 
8. What does `lsblk -f` show?  lsblk -f shows block devices, partitions, and their filesystems in a tree view. 
9. What does `blkid` show? blkid shows block device attributes like UUID, TYPE, and LABEL. 
10. Why use UUIDs instead of names such as `/dev/sdb1`? UUIDS are unique identifiers that remain stable even if devices change names. 
