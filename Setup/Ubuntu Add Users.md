
Use this account model:

```text
admin account     = used only for sudo/admin work
daily account     = used for normal SSH/login
elliot account   = standard user, no sudo
```

Do **not** let everyone use the installer-created admin account for daily work. That is sloppy.

## Recommended accounts

Example names:

```text
his admin:       isaac-admin
his daily:       isaac
your admin:      takashi-admin
your daily:      takashi
elliot daily:   elliot
```

Replace names as needed.

## 1. Create standard daily accounts

Run from the existing admin account:

```bash
sudo adduser isaac
sudo adduser takashi
sudo adduser elliot
```

These users are standard users by default on Ubuntu.

Check:

```bash
id isaac
id takashi
id elliot
```

They should **not** be in the `sudo` group.

## 2. Create your admin account

```bash
sudo adduser takashi-admin
sudo usermod -aG sudo takashi-admin
```

Check:

```bash
id takashi-admin
```

You should see `sudo` in the groups.

## 3. Decide what to do with the installer-created admin account

There are two good choices.

### Option A: Keep it as his admin account

Rename it mentally as “admin only.” Do not use it for daily SSH.

Example:

```text
installer account = his admin account
isaac            = his daily account
```

This is simplest.

### Option B: Create a separate named admin account for him

```bash
sudo adduser isaac-admin
sudo usermod -aG sudo isaac-admin
```

Then later disable sudo on the installer account, but only after testing the new admin accounts.

Check sudo users:

```bash
getent group sudo
```

## 4. Set up SSH keys per person

Each person should have their own SSH key from their own computer.

From his computer:

```bash
ssh-copy-id isaac@db01
```

From your computer:

```bash
ssh-copy-id takashi@db01
ssh-copy-id takashi-admin@db01
```

For elliot:

```bash
ssh-copy-id elliot@db01
```

Then test:

```bash
ssh isaac@db01
ssh takashi@db01
ssh takashi-admin@db01
```

## 5. Disable password SSH only after keys work

Edit SSH config:

```bash
sudoedit /etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart:

```bash
sudo systemctl restart ssh
```

Keep one existing SSH session open while testing a new one. Getting locked out here is a beginner mistake.

## 6. Use sudo only from admin accounts

From daily account:

```bash
sudo apt update
```

Expected result:

```text
isaac is not in the sudoers file
```

Good. That means the standard account is actually standard.

From admin account:

```bash
sudo apt update
```

Expected: works.

## 7. Optional: allow daily users to inspect logs without sudo

For learning, he may need to read system logs. Add his daily account to `adm`:

```bash
sudo usermod -aG adm isaac
sudo usermod -aG adm takashi
```

Then log out and back in.

This lets him read many logs without giving full admin power.

I would **not** add elliot to `adm` unless he needs it.

## 8. Optional: create a shared project group

If multiple people need access to project files:

```bash
sudo groupadd homelab
sudo usermod -aG homelab isaac
sudo usermod -aG homelab takashi
```

Create shared directory:

```bash
sudo mkdir -p /srv/homelab
sudo chown root:homelab /srv/homelab
sudo chmod 2775 /srv/homelab
```

The `2` in `2775` keeps new files group-owned by `homelab`.

## 9. Account policy I would enforce

Use this rule:

```text
Daily work:       standard account
Installing stuff: admin account with sudo
Running services: dedicated service account
Database app:     database user, not Linux login user
```

So later, the Clojure app should not run as `isaac` or `takashi`. It should run as something like:

```bash
sudo adduser --system --group --home /opt/clojure-crud appuser
```

That is the professional pattern.

## Clean target state

```text
isaac-admin      sudo allowed
isaac            standard daily user

takashi-admin     sudo allowed
takashi           standard daily user

elliot            standard daily user only

appuser           system/service user, no login
```

That is a clean setup. It teaches separation of privileges instead of turning every Linux account into an all-powerful mess.