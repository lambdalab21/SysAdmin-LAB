
Use a **layered workflow**. Do not randomly jump between `man`, `info`, `--help`, Stack Overflow, and tutorials. First identify what kind of thing you are dealing with, then choose the right reference.

## The basic workflow

```text
1. Identify what the command actually is.
2. Get quick syntax.
3. Read the proper reference page.
4. Search inside the docs for the exact option/behavior.
5. Test safely.
6. Record what you learned.
```

## 1. First: identify the command

Before reading documentation, ask: “What am I actually running?”

```bash
type date
type cd
type echo
type test
type systemctl
```

Examples:

```bash
type cd
```

Possible result:

```text
cd is a shell builtin
```

Then use:

```bash
help cd
```

Another example:

```bash
type date
```

Possible result:

```text
date is /usr/bin/date
```

Then use:

```bash
date --help
man date
info coreutils 'date invocation'
```

This step prevents a common beginner mistake: reading the wrong documentation.

## 2. For quick use: try `--help`

Use this when you need a fast reminder.

```bash
date --help | less
cp --help | less
ls --help | less
systemctl --help | less
```

For many GNU commands, `--help` is better than the man page for discovering options quickly.

Example:

```bash
date --help | less
```

Search inside `less`:

```text
/%s
```

You should find the meaning of `+%s`.

## 3. For serious reference: use `man`

Use `man` when you need the standard reference.

```bash
man date
man cp
man ssh
man systemctl
man journalctl
```

Inside `man`, do not read from top to bottom unless the page is short. Search.

```text
/FORMAT
/%s
/-r
/EXAMPLES
/SEE ALSO
```

Useful keys:

```text
q      quit
/word  search
n      next match
N      previous match
Space  next page
b      previous page
g      top
G      bottom
```

## 4. For GNU commands: check `info`

Use `info` especially for GNU Coreutils commands:

```bash
info coreutils 'date invocation'
info coreutils 'ls invocation'
info coreutils 'cp invocation'
info coreutils 'chmod invocation'
```

GNU commands include many everyday tools:

```text
date
ls
cp
mv
rm
chmod
chown
cat
sort
uniq
cut
tr
wc
```

For these, the real full manual may be in `info`, not `man`.

## 5. For shell built-ins: use `help`

Commands like these are often shell built-ins:

```text
cd
alias
export
read
history
jobs
fg
bg
set
test
[
```

Use:

```bash
help cd
help export
help read
help test
```

For more detail:

```bash
man bash
```

But `man bash` is huge, so search directly:

```text
/SHELL BUILTIN COMMANDS
/read
/export
```

## 6. For config files: use section 5 man pages

This is important for server work.

Command:

```bash
man ssh
```

Config file:

```bash
man 5 ssh_config
man 5 sshd_config
```

Systemd unit files:

```bash
man systemctl
man systemd.service
man systemd.unit
```

Sudo:

```bash
man sudo
man sudoers
man 5 sudoers
```

Nginx:

```bash
man nginx
less /etc/nginx/nginx.conf
ls /usr/share/doc/nginx
```

Do not only read command docs. Server behavior often lives in config-file docs.

## 7. For package-specific notes: check `/usr/share/doc`

Use this when the command behaves differently on Debian/Ubuntu/Alma/Rocky because of packaging.

Debian/Ubuntu:

```bash
ls /usr/share/doc/openssh-server
ls /usr/share/doc/nginx
ls /usr/share/doc/coreutils
```

Read files:

```bash
less /usr/share/doc/nginx/README.Debian
zless /usr/share/doc/coreutils/changelog.Debian.gz
```

This is useful when tutorials say one thing, but your distro behaves differently.

## 8. For unknown commands: use `apropos`

When you do not know the command name:

```bash
apropos password
apropos user
apropos network
apropos ssh
man -k firewall
```

Then open the relevant result:

```bash
man useradd
man passwd
man sshd_config
```

## A practical decision tree

Use this:

```text
I know the command.
    ↓
Run: type command
    ↓
If shell builtin:
    use help command
    maybe man bash
    ↓
If external command:
    use command --help | less
    then man command
    ↓
If GNU tool and man is too short:
    use info coreutils 'command invocation'
    ↓
If config-related:
    use man 5 config_file or man related unit/config page
    ↓
If distro-specific behavior:
    check /usr/share/doc/package
```

## Example workflow: `date +%s`

```bash
type date
```

Result:

```text
date is /usr/bin/date
```

Quick docs:

```bash
date --help | less
```

Search:

```text
/%s
```

Then:

```bash
man date
```

If not enough:

```bash
info coreutils 'date invocation'
```

Test:

```bash
date +%s
date -d @0
date -d @1710000000
```

Then write down:

```text
date +%s prints seconds since Unix epoch.
The +FORMAT syntax uses percent escapes.
GNU date documents this more fully in info/coreutils.
```

That is effective learning.

## Example workflow: adding a user

Suppose a tutorial says:

```bash
sudo usermod -aG sudo john
```

Do not run it blindly.

Check command:

```bash
type usermod
```

Quick help:

```bash
usermod --help | less
```

Man page:

```bash
man usermod
```

Search:

```text
/-a
/-G
```

You should learn:

```text
-G sets supplementary groups.
-a appends instead of replacing.
Without -a, -G can remove the user from other supplementary groups.
```

Then inspect current state:

```bash
id john
groups john
```

Run command:

```bash
sudo usermod -aG sudo john
```

Verify:

```bash
id john
groups john
```

This is the correct habit: **read → predict → run → verify**.

## Example workflow: SSH server config

```bash
type sshd
man sshd
man 5 sshd_config
```

Search:

```text
/PasswordAuthentication
/PubkeyAuthentication
/PermitRootLogin
```

Then inspect actual config:

```bash
sudo less /etc/ssh/sshd_config
```

Check effective config:

```bash
sudo sshd -T | less
```

Test config before restarting:

```bash
sudo sshd -t
```

Restart only after that:

```bash
sudo systemctl restart ssh
```

or on some systems:

```bash
sudo systemctl restart sshd
```

Verify:

```bash
systemctl status ssh
ss -tlnp | grep :22
```

This is how infrastructure people work. They do not just edit and hope.

## The serious learning pattern

For every unfamiliar command, make a small note like this:

```text
Command:
Purpose:
Type: builtin / external / admin command
Important options:
Dangerous options:
Files affected:
How to verify:
Related man pages:
Example:
```

Example:

```text
Command: usermod
Purpose: modify user account
Type: external admin command
Important options: -a, -G, -s, -L, -U
Dangerous options: -G without -a
Files affected: /etc/passwd, /etc/shadow, /etc/group
How to verify: id USER, groups USER, getent passwd USER
Related man pages: useradd(8), userdel(8), passwd(5), group(5)
Example: sudo usermod -aG sudo john
```

That note is worth more than copying ten tutorials.

## What your son should practice

Give him this rule:

```text
Before running any command with sudo, he must explain every option in the command.
```

Not perfectly. But enough.

For example:

```bash
sudo chown -R john:www-data /var/www/site
```

He should be able to answer:

```text
What does chown do?
What does -R do?
Who is john?
What is www-data?
What path is affected?
How can I verify the result?
What is the risk if the path is wrong?
```

That last question matters. `-R` plus `sudo` plus a wrong path is how beginners damage systems.

## Bottom line

The workflow is:

```text
type
→ --help or help
→ man
→ info when GNU docs are deeper
→ config man pages
→ /usr/share/doc for distro-specific notes
→ test safely
→ verify
→ write a short note
```

The goal is not to memorize commands. The goal is to stop being a passenger. Every command should become something he can inspect, predict, run, and verify.



[Resource](https://chatgpt.com/s/t_6a15c739afb48191ab81d56839920daa)
