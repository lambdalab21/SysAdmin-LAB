#Author/Lucas #ssh #SSH-Mastery #network 
# SSH Mastery Reading Guide — Chapter 7: SSH Keys

**Book:** Michael W. Lucas, *SSH Mastery: OpenSSH, PuTTY, Tunnels and Keys*, 2nd edition  
**Student focus:** Linux/OpenSSH first. Windows GUI tools are optional later.

## Read / skim / skip

### Read now

- Generating key pairs
- Public key versus private key
- Passphrases
- `authorized_keys`
- File permissions
- Key-based login
- SSH agent basics

### Skim

- Key-distribution at scale if the chapter touches it lightly
- Old key types unless Lucas says they matter historically

### Skip for now

- Do not skip security warnings. Do not rush into passwordless automation yet. Automation belongs later, after key safety is understood.

## Feynman analogy

A public key is a lock you install on servers. Your private key is the only key that opens that lock. A passphrase protects the private key if stolen. An agent holds the unlocked key for a working session.

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

- Which file is private?
- Which file is public?
- Where does the public key go on the server?
- Why does `authorized_keys` authorize a user, not a whole machine?
- Why should private-key permissions be strict?
- What does an SSH agent actually hold?

## Stop-and-think questions

After each major section, answer at least one:

- Why should a private key never be copied casually?
- Why is an empty passphrase convenient but risky?
- What breaks if `.ssh` permissions are too open?
- How do you prove key authentication worked instead of password authentication?
- What is the difference between possession of a key file and authorization to use an account?

## Minimal observation

This is not a giant lab. Run only enough to connect the reading to a real machine.

- Generate a lab key only if needed: `ssh-keygen -t ed25519 -f ~/.ssh/app01_lab_ed25519`.
- Print the public key, not the private key: `cat ~/.ssh/app01_lab_ed25519.pub`.
- Install the public key safely on `app01` using `ssh-copy-id` if available, or manual editing with care.
- Test with `ssh -i ~/.ssh/app01_lab_ed25519 -v user@app01 true`.
- Inspect `ssh-agent` with `ssh-add -l`.

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

Write a key-authentication explanation that includes private key, public key, passphrase, `authorized_keys`, permissions, and agent.

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
