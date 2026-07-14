# Day 6 — LVM Concepts and Safe Lab

## Read before lab

Read the Ch. 4 section on the Logical Volume Manager.

## Goal

Understand LVM as an added abstraction layer:

```text
physical volume
→ volume group
→ logical volume
→ filesystem
→ mount point
```

## Why LVM matters

LVM can make storage management more flexible by allowing logical volumes to be created, resized, and managed above physical devices. It is common on servers.

Do not let this become magic. Draw the layers.

## Lab setup

Use a fake disk file.

```bash
mkdir -p ~/hlw-ch4-lvm
cd ~/hlw-ch4-lvm

fallocate -l 1G lvm-disk.img
LOOPDEV=$(sudo losetup --find --show lvm-disk.img)
echo "$LOOPDEV"
```

## Lab 1: Create LVM layers

Create a physical volume:

```bash
sudo pvcreate "$LOOPDEV"
```

Create a volume group:

```bash
sudo vgcreate hlwvg "$LOOPDEV"
```

Create a logical volume:

```bash
sudo lvcreate -L 300M -n hlwlv hlwvg
```

Inspect:

```bash
sudo pvs
sudo vgs
sudo lvs
lsblk
```

## Lab 2: Put a filesystem on the logical volume

```bash
sudo mkfs.ext4 /dev/hlwvg/hlwlv
sudo mkdir -p /mnt/hlw-lvm
sudo mount /dev/hlwvg/hlwlv /mnt/hlw-lvm
```

Use it:

```bash
echo "hello LVM" | sudo tee /mnt/hlw-lvm/hello.txt
df -h /mnt/hlw-lvm
findmnt /mnt/hlw-lvm
```

## Lab 3: Explain the layers

Write this:

```text
Loop device:
Physical volume:
Volume group:
Logical volume:
Filesystem type:
Mount point:
```

## Cleanup

Unmount first:

```bash
sudo umount /mnt/hlw-lvm
```

Remove LVM objects:

```bash
sudo lvremove -y /dev/hlwvg/hlwlv
sudo vgremove -y hlwvg
sudo pvremove -y "$LOOPDEV"
sudo losetup -d "$LOOPDEV"
```

## Questions

1. What problem does LVM solve?
2. What is a physical volume?
3. What is a volume group?
4. What is a logical volume?
5. Does LVM replace filesystems?
6. Why does a logical volume still need `mkfs` before mounting?
7. Why is LVM an abstraction layer, not a filesystem?
