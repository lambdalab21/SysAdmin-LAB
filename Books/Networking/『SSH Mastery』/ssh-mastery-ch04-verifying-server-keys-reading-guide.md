# SSH Mastery Reading Guide — Chapter 4: Verifying Server Keys

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- First-time host-key prompt
- `known_hosts`
- Host-key fingerprints
- Changed host-key warnings
- Man-in-the-middle risk
- How to verify a host key in a lab

### Skim

- Large-organization host-key distribution methods if they are beyond the home lab

### Skip for now

- PuTTY-specific host-key screens for now unless he uses PuTTY.

## Feynman analogy

A host key is like recognizing the remote server’s face. The first time, you decide whether to trust that face. Later, if the face suddenly changes, do not casually ignore it.

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

- What is a server host key?
- Where does the client remember server keys?
- What does a changed host-key warning mean?
- How could a server rebuild legitimately change a host key?
- How could an attacker abuse blind acceptance?

## Stop-and-think questions

After each major section, answer at least one:

- Is the host key authenticating the server or the user?
- Why is first connection trust weaker than verified trust?
- What should you do before deleting a `known_hosts` entry?
- Why is `StrictHostKeyChecking no` dangerous as a habit?

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- Run `ssh-keygen -F app01`.
- Run `ssh-keyscan app01` only against your own lab host.
- On the server, inspect public host keys with `ls -l /etc/ssh/ssh_host_*.pub`.
- Compare fingerprints if appropriate: `ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub` on the server.

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

Write a decision tree: `What to do when SSH says the host key changed.`

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
