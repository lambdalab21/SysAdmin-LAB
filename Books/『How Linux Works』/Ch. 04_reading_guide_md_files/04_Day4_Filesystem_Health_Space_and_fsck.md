# Day 4 — Filesystem Space, Inodes, and fsck

## Read before lab

Read the Ch. 4 sections on filesystem capacity, filesystem checking, and filesystem maintenance.

## Goal

Understand that a filesystem can fail or become full in multiple ways:

```text
data blocks full
inode exhaustion
unclean shutdown or corruption
wrong mount state for checking
```

## Lab 1: Compare `df` and `du`

Run on your home directory:

```bash
df -h ~
du -sh ~
du -sh ~/.cache 2>/dev/null || true
```

Answer:

1. Which command reports filesystem free space?
2. Which command reports space used by a directory tree?
3. Why can `du` and `df` disagree?

## Lab 2: Inode awareness

Run:

```bash
df -i
```

Answer:

1. What are inodes?
2. Why can a filesystem be unable to create files even if it has data blocks free?
3. What kind of workload creates many small files?

Do not intentionally exhaust inodes on a normal system.

## Lab 3: Use `fsck` only when unmounted

Use the loopback filesystem from Day 3.

Create and mount it, then try checking while mounted:

```bash
# assuming PART is set and mounted at /mnt/hlw-ch4
sudo fsck "$PART"
```

It should warn you. Do not force repair on a mounted filesystem.

Unmount:

```bash
sudo umount /mnt/hlw-ch4
```

Now check:

```bash
sudo fsck -f "$PART"
```

Answer:

1. Why should a filesystem usually be unmounted before `fsck`?
2. What could happen if you repair a live mounted filesystem?
3. Why does Linux sometimes check filesystems at boot?

## Lab 4: Read-only mount

Mount read-only:

```bash
sudo mount -o ro "$PART" /mnt/hlw-ch4
```

Try to write:

```bash
echo "test" | sudo tee /mnt/hlw-ch4/readonly-test.txt
```

It should fail.

Inspect:

```bash
findmnt -o TARGET,SOURCE,FSTYPE,OPTIONS /mnt/hlw-ch4
```

Unmount:

```bash
sudo umount /mnt/hlw-ch4
```

## Retrieval questions

1. What does `df -h` tell you?
2. What does `du -sh` tell you?
3. What does `df -i` tell you?
4. Why is `fsck` not just another harmless read command?
5. What does a read-only mount prove?
