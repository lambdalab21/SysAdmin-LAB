# SSH Mastery Reading Guide — Chapter 2: Common Configuration

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- User SSH config file
- Host aliases
- User, HostName, Port, IdentityFile
- System-wide config versus user config
- How OpenSSH chooses settings

### Skim

- Options he does not understand yet
- System-wide configuration details unless he administers the machine

### Skip for now

- PuTTY session-configuration sections for now if he is using Linux/OpenSSH. Windows-client material can wait.

## Feynman analogy

`~/.ssh/config` is like an address book plus instructions. Instead of typing the full address, username, port, and key every time, you create a named entry and let SSH use it.

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

- Where is the user SSH config file?
- Why use a Host alias?
- What does `HostName` mean?
- What does `User` mean?
- What does `IdentityFile` mean?
- How can config make commands safer and less repetitive?

## Stop-and-think questions

After each major section, answer at least one:

- What is the difference between `Host app01` and `HostName 192.168.1.20`?
- Why might a wrong config entry make SSH fail even when the network is fine?
- Why should config files have careful permissions?
- What is the risk of copying random SSH config examples?

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- Run `test -f ~/.ssh/config && cat ~/.ssh/config || echo 'No user config yet'`.
- Create or inspect a simple Host alias only if appropriate.
- Run `ssh -G app01 | head -40` to see effective OpenSSH client configuration if supported.

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

Create a safe example SSH config entry for `app01`. Explain every line. Do not include passwords.

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
