# Day 7 — Review, Troubleshooting, and Exit Test

## Goal

Prove that he understands storage layers and can troubleshoot basic disk/filesystem problems without random commands.

## Review diagram

He should draw this from memory:

```text
disk or loop device
→ partition table
→ partition
→ filesystem
→ mount point
→ files
```

And this:

```text
physical volume
→ volume group
→ logical volume
→ filesystem
→ mount point
```

## Mystery Challenge 1: Why does `df` not show my new filesystem?

Setup:

Create a fake disk, partition it, and run `mkfs`, but do not mount it.

Ask:

```text
Why does `df -h` not show the new filesystem?
```

Expected explanation:

```text
`df` shows mounted filesystems. A filesystem can exist on a device but not appear in `df` until mounted.
```

Useful tools:

```bash
lsblk -f
blkid
findmnt
df -h
```

## Mystery Challenge 2: Why can’t I unmount?

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

## Mystery Challenge 3: Why is writing denied?

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

## Mystery Challenge 4: Why is the web server using the wrong files?

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

1. What is the difference between a disk and a partition?
2. What is the difference between a partition and a filesystem?
3. What does `mkfs` do?
4. What does `mount` do?
5. What does `umount` do?
6. What does `df -h` show?
7. What does `du -sh` show?
8. What does `lsblk -f` show?
9. What does `blkid` show?
10. Why use UUIDs instead of names such as `/dev/sdb1`?
11. Why should `fsck` usually run on an unmounted filesystem?
12. What is swap?
13. Why is swap not an ordinary filesystem?
14. What is LVM?
15. What layer does LVM add?
16. Why should a web server’s document root be backed up before using `rsync --delete`?
17. Why is experimenting with storage commands dangerous on the wrong device?
18. How would you safely practice partitioning?

## Minimum completion standard

He is ready to move on when he can explain:

```text
I can create a fake disk file, attach it as a loop device, partition it, create a filesystem, mount it, write files, unmount it, check it, and clean it up — while explaining each layer.
```

If he cannot explain the layers, he should not move on to LVM or real-disk work.
