# SSH Mastery Reading Guide — Chapter 6: Copying Files over SSH

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- `scp` basics
- `sftp` basics
- Copy direction
- Remote path syntax
- Why SSH file transfer is safer than plaintext FTP
- When to prefer `rsync` later even if this chapter focuses on SSH tools

### Skim

- Advanced file-copy flags not needed yet

### Skip for now

- WinSCP sections for now unless he is using Windows. GUI file-transfer client material can wait.

## Feynman analogy

`scp` is like saying, 'take this file from here to there.' `sftp` is like opening a secure file-manager session on the remote machine. Both ride through the SSH tunnel.

Now write where the analogy fails:

```text
The analogy helps because:

The analogy fails because:

The real SSH idea is:
```

## Before reading

Answer before opening the chapter:

```text
What do I already think this chapter is about?

What might I be overconfident about?

What command or file have I seen before but not really understood?
```

## Important ideas to hunt for

Do not read mindlessly. Look for answers to these:

- What does the colon mean in `user@app01:/tmp/file`?
- How do you upload versus download?
- What is the difference between `scp` and `sftp`?
- Why is `sftp` not traditional FTP?
- What mistake could overwrite or copy to the wrong place?

## Stop-and-think questions

After each major section, answer at least one:

- Which side is local and which side is remote?
- Why verify the destination path before copying recursively?
- What does `scp -r` do?
- Why is file transfer authentication plus authorization, not just networking?

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- Create `message.txt` locally and copy it to `/tmp/` on `app01`.
- Copy `/etc/hostname` from `app01` back to the client.
- Open an `sftp` session and test `pwd`, `lpwd`, `ls`, `lls`, `put`, and `get`.

Use this format:

```text
Question I am testing:

Command or file inspected:

Expected evidence:

Actual evidence:

Conclusion:

What this does NOT prove:
```

## Beginner traps

- Saying “I know SSH” because you can type `ssh user@host`.
- Running commands from the book without knowing what problem they solve.
- Treating security warnings as annoying messages.
- Copying configuration examples before understanding the risk.
- Changing server configuration without a second login session or console access.

## Required written answer

Create a table showing upload, download, recursive copy, and interactive transfer examples. Explain each path.

## Self-grade

| Skill | 0 = weak | 1 = partial | 2 = solid | Notes |
|---|---:|---:|---:|---|
| I can explain the chapter simply |  |  |  |  |
| I can identify the important files/commands |  |  |  |  |
| I know what to skip for now |  |  |  |  |
| I can name one beginner mistake |  |  |  |  |
| I can connect this to `app01` or `db01` |  |  |  |  |

## Rule

Do not move on because the words felt familiar. Move on only when you can explain the idea, point to evidence, and state what a careless beginner would get wrong.
