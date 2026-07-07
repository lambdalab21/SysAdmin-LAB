# SSH Mastery Reading Guide — Chapter 0: The Problem

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- Why SSH exists
- What problems SSH solves
- Why remote administration needs encryption and identity checks
- Why random online SSH advice can be dangerous

### Skim

- Historical background that does not affect current OpenSSH use

### Skip for now

- Nothing major. This is context.

## Feynman analogy

SSH is like a locked, verified service tunnel to a remote machine. Telnet is like shouting your password across a parking lot.

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

- What problem existed before SSH became common?
- What two broad things does SSH protect?
- Why is 'I can log in' not the same as 'I understand SSH'?
- What bad advice should a beginner distrust?

## Stop-and-think questions

After each major section, answer at least one:

- What is the difference between remote login and secure remote login?
- What does SSH protect against that plaintext does not?
- What does SSH not automatically protect against?
- Why is a changed host-key warning serious?

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- Run `ssh -V` and record the OpenSSH version.
- Run `which ssh scp sftp ssh-keygen ssh-agent`.
- Write which SSH-related tools already exist on the machine.

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

Write a short note: `What SSH is for, what it protects, and what it does not magically fix.`

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
