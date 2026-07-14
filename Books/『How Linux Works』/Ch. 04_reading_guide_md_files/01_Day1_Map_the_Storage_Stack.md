# Day 1 — Map the Storage Stack

## Read before lab

Read the opening part of Ch. 4 through the sections on viewing partition tables and navigating disk/filesystem commands.

Focus on the big picture:

```text
disk device
partition table
partition
filesystem
mount point
directory tree
```

## What to pay attention to

Do not memorize command output yet. Learn what layer each command reveals.

| Command | What it helps reveal |
|---|---|
| `lsblk` | block devices and their tree relationships |
| `blkid` | filesystem type, UUID, labels |
| `df -h` | mounted filesystems and space usage |
| `du -sh` | disk usage by files/directories |
| `findmnt` | current mount relationships |
| `mount` | mounted filesystems and options |
| `fdisk -l` | disk/partition table information |

## Lab 1: Inspect the current machine

Run:

```bash
lsblk
lsblk -f
blkid
findmnt
df -h
du -sh ~
```

Answer:

1. Which devices are disks?
2. Which entries are partitions?
3. Which filesystems are currently mounted?
4. Which filesystem is mounted at `/`?
5. What is the difference between `df` and `du`?
6. Which command shows UUIDs?

## Lab 2: Compare `/dev`, `/proc`, and mounted filesystems

Run:

```bash
ls /dev | head
mount | head
findmnt /proc
findmnt /dev
findmnt /
```

Answer:

1. Is `/proc` an ordinary disk filesystem?
2. Is `/dev` just an ordinary directory?
3. Why does the same unified directory tree contain different filesystem types?
4. Why does `mount` matter if everything appears under `/`?

## Lab 3: Explain the chain

Pick the root filesystem from `findmnt /`.

Write this in your notes:

```text
For the root filesystem:
source device:
filesystem type:
mount point:
available space:
UUID, if shown:
```

## Retrieval questions

Answer without notes:

1. What is a block device?
2. What is a partition?
3. What is a filesystem?
4. What is a mount point?
5. Why does Linux not use Windows-style drive letters?
6. Why can a filesystem be full even if the machine has another disk with free space?

## Minimum exit criteria

He can explain this clearly:

```text
`/var/www` is just a directory path, but it may reside on a filesystem mounted from a particular block device or partition.
```
