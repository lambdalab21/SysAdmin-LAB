# Day 3 — Create, Mount, Use, and Unmount a Filesystem

## Read before lab

Read the Ch. 4 sections on creating filesystems, mounting, unmounting, and filesystem UUIDs.

## Goal

Understand that a filesystem becomes usable only after it is mounted somewhere in the directory tree.

## Lab setup

Recreate or reuse the fake disk from Day 2.

```bash
mkdir -p ~/hlw-ch4
cd ~/hlw-ch4

fallocate -l 512M fake-disk.img
LOOPDEV=$(sudo losetup --find --partscan --show fake-disk.img)
echo "$LOOPDEV"
```

If the image does not already have a partition, create one as in Day 2.

Find the partition:

```bash
lsblk "$LOOPDEV"
```

Set:

```bash
PART=${LOOPDEV}p1
echo "$PART"
```

If your loop device naming differs, set `PART` manually.

## Lab 1: Create an ext4 filesystem

```bash
sudo mkfs.ext4 "$PART"
```

Inspect:

```bash
lsblk -f "$LOOPDEV"
sudo blkid "$PART"
```

Answer:

1. What filesystem type appears?
2. What UUID was assigned?
3. Is it mounted yet?

## Lab 2: Mount it

Create a mount point:

```bash
sudo mkdir -p /mnt/hlw-ch4
```

Mount:

```bash
sudo mount "$PART" /mnt/hlw-ch4
```

Inspect:

```bash
findmnt /mnt/hlw-ch4
df -h /mnt/hlw-ch4
mount | grep hlw-ch4
```

Create a file:

```bash
echo "hello from mounted filesystem" | sudo tee /mnt/hlw-ch4/hello.txt
cat /mnt/hlw-ch4/hello.txt
```

## Lab 3: Unmount and observe

```bash
sudo umount /mnt/hlw-ch4
```

Inspect:

```bash
findmnt /mnt/hlw-ch4 || true
ls /mnt/hlw-ch4
```

Answer:

1. Did `/mnt/hlw-ch4` disappear?
2. Did the file appear after unmounting?
3. Where did the file actually live while mounted?
4. What is the difference between the mount point directory and the mounted filesystem?

Remount:

```bash
sudo mount "$PART" /mnt/hlw-ch4
ls /mnt/hlw-ch4
```

## Lab 4: Try to unmount while busy

In one terminal:

```bash
cd /mnt/hlw-ch4
```

In another terminal:

```bash
sudo umount /mnt/hlw-ch4
```

It may report that the target is busy.

Find why:

```bash
sudo lsof +f -- /mnt/hlw-ch4
```

Then leave the directory in the first terminal:

```bash
cd ~
```

Try again:

```bash
sudo umount /mnt/hlw-ch4
```

## Cleanup

```bash
sudo umount /mnt/hlw-ch4 2>/dev/null || true
sudo losetup -d "$LOOPDEV"
```

## Retrieval questions

1. What does `mkfs.ext4` do?
2. What does `mount` do?
3. What does `umount` do?
4. Why can a mount point be an ordinary empty directory before mounting?
5. Why might unmount fail with “busy”?
6. What does UUID help with?
