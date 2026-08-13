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

1. What problem does LVM solve? LVM solves inflexible disk management by letting the user resize, add, or move storage without downtime or divisions. 
2. What is a physical volume? A physical volume(or a PV) is a physical disk initialized for use in LVM. 
3. What is a volume group? A volume group( or a VG) is a pool of storage that combines one of the more physical volumes. 
4. What is a logical volume? A logical volume( or a LV) is a virtual partition created from space in a volume group. 
5. Does LVM replace filesystems? No, LVM works on top of physical storage and still requires a filesystem. 
6. Why does a logical volume still need `mkfs` before mounting? Because a logical volume is just a block device, it needs a filesystem to organize files and directories.
7. Why is LVM an abstraction layer, not a filesystem? LVM provides flexible block device management but does not handle file storage. 
