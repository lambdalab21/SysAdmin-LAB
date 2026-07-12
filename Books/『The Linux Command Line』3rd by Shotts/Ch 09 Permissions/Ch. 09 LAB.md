#Book/The-Linux-Command-Line #Author/Shotts 

These labs are for `playground01`. They are deliberately more substantial than the short drills. The student should predict the outcome before running each command, observe the actual result, and explain any mismatch.

Chapter 9 is not merely about memorizing `chmod` numbers. The important ideas are ownership, groups, file modes, directory modes, default permissions, identity changes, and safe privilege escalation. Author/Shotts’s permissions lesson distinguishes owner, group, and others; explains octal `chmod`; and emphasizes that `r`, `w`, and `x` behave differently on directories. ([LinuxCommand](https://linuxcommand.org/lc3_lts0090.php?utm_source=chatgpt.com "Learning the shell - Lesson 9: Permissions"))

# Chapter 9 Labs: Permissions

## Safety boundary

Use only:

```text
/srv/ch09-lab
```

Do not run recursive `chmod` or `chown` commands against `/`, `/home`, `/etc`, or `/var`.

The commands assume the regular account created during installation has `sudo` privileges.

---

# One-time setup

The setup creates three disposable users:

|User|Role|
|---|---|
|`alice`|File owner and project member|
|`bob`|Project member|
|`charlie`|Outsider|

Create a shared group and the test users:

```bash
sudo groupadd -f project

id alice >/dev/null 2>&1 || sudo useradd -m alice
id bob >/dev/null 2>&1 || sudo useradd -m bob
id charlie >/dev/null 2>&1 || sudo useradd -m charlie

sudo usermod -aG project alice
sudo usermod -aG project bob
```

Create the lab directory:

```bash
sudo rm -rf /srv/ch09-lab
sudo install -d -o alice -g project -m 0750 /srv/ch09-lab
```

Inspect the result:

```bash
ls -ld /srv/ch09-lab
id alice
id bob
id charlie
```

Expected pattern:

```text
drwxr-x--- alice project /srv/ch09-lab
```

The setup commands `groupadd`, `useradd`, and `usermod` are scaffolding. The student does not need to master them yet.

---

# Lab 1: Identify users and groups

## Goal

Understand the identities Linux uses when deciding whether access is permitted.

Run:

```bash
id
id alice
id bob
id charlie
```

Inspect the account and group databases:

```bash
grep -E '^(alice|bob|charlie):' /etc/passwd
grep '^project:' /etc/group
```

Try:

```bash
sudo -u alice id
sudo -u bob id
sudo -u charlie id
```

## Questions

1. What are Alice’s user ID and primary group ID?
    
2. Which users belong to `project`?
    
3. Is Charlie a member of `project`?
    
4. Why does Linux use numeric IDs internally even though commands display names?
    
5. When a file belongs to group `project`, which of the three users may receive the group permissions?
    

## Core lesson

A username is not the permission itself. Linux compares the process identity with the file’s owner and group.

---

# Lab 2: Read an `ls -l` permission string

## Goal

Learn to interpret file type, owner permissions, group permissions, and permissions for others.

Create three files:

```bash
sudo -u alice touch /srv/ch09-lab/public.txt
sudo -u alice touch /srv/ch09-lab/project.txt
sudo -u alice touch /srv/ch09-lab/private.txt

sudo chmod 644 /srv/ch09-lab/public.txt
sudo chmod 640 /srv/ch09-lab/project.txt
sudo chmod 600 /srv/ch09-lab/private.txt
```

Inspect them:

```bash
ls -l /srv/ch09-lab
```

Expected modes:

```text
-rw-r--r-- public.txt
-rw-r----- project.txt
-rw------- private.txt
```

Test access:

```bash
sudo -u alice cat /srv/ch09-lab/public.txt
sudo -u bob cat /srv/ch09-lab/public.txt
sudo -u charlie cat /srv/ch09-lab/public.txt
```

Then test:

```bash
sudo -u alice cat /srv/ch09-lab/project.txt
sudo -u bob cat /srv/ch09-lab/project.txt
sudo -u charlie cat /srv/ch09-lab/project.txt
```

Finally:

```bash
sudo -u alice cat /srv/ch09-lab/private.txt
sudo -u bob cat /srv/ch09-lab/private.txt
sudo -u charlie cat /srv/ch09-lab/private.txt
```

## Questions

1. Why can Bob read `project.txt`?
    
2. Why can Charlie read `public.txt` but not `project.txt`?
    
3. Why can only Alice read `private.txt`?
    
4. In `-rw-r-----`, which three characters apply to Bob?
    
5. What does the first `-` mean?
    

## Core lesson

Read a mode in four sections:

```text
-   rw-   r--   ---
│    │     │     └── others
│    │     └──────── group
│    └────────────── owner
└─────────────────── file type
```

---

# Lab 3: Compare numeric and symbolic `chmod`

## Goal

Understand that numeric notation replaces the complete ordinary mode, while symbolic notation can make a targeted change. GNU `chmod` accepts either an octal mode or symbolic changes. ([man7.org](https://man7.org/linux/man-pages/man1/chmod.1.html?utm_source=chatgpt.com "chmod(1) - Linux manual page"))

Create a file:

```bash
sudo -u alice touch /srv/ch09-lab/mode-test.txt
sudo chmod 600 /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

Add group-read permission symbolically:

```bash
sudo chmod g+r /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

Add read permission for others:

```bash
sudo chmod o+r /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

Remove write permission from the owner:

```bash
sudo chmod u-w /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

Replace the entire mode numerically:

```bash
sudo chmod 640 /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

Replace it symbolically:

```bash
sudo chmod u=rw,g=r,o= /srv/ch09-lab/mode-test.txt
ls -l /srv/ch09-lab/mode-test.txt
```

## Questions

1. What changed after `chmod g+r`?
    
2. Did `chmod g+r` modify Alice’s permissions?
    
3. What does `o=` mean?
    
4. What is the difference between `+` and `=`?
    
5. When is symbolic notation safer than numeric notation?
    

## Core lesson

Use numeric notation when setting a known complete mode:

```bash
chmod 640 file
```

Use symbolic notation when changing one detail:

```bash
chmod g+w file
```

---

# Lab 4: Understand execute permission on files

## Goal

See that reading a script and executing a script are separate permissions.

Create a script:

```bash
sudo -u alice bash -c 'cat > /srv/ch09-lab/hello.sh << "EOF"
#!/usr/bin/env bash
echo "Hello from the script"
EOF'
```

Make it readable but not executable:

```bash
sudo chmod 644 /srv/ch09-lab/hello.sh
ls -l /srv/ch09-lab/hello.sh
```

Try to execute it:

```bash
sudo -u alice /srv/ch09-lab/hello.sh
```

Expected result:

```text
Permission denied
```

Run it by explicitly invoking Bash:

```bash
sudo -u alice bash /srv/ch09-lab/hello.sh
```

Now add execute permission:

```bash
sudo chmod 755 /srv/ch09-lab/hello.sh
sudo -u alice /srv/ch09-lab/hello.sh
```

## Questions

1. Why did direct execution fail when the mode was `644`?
    
2. Why did `bash /srv/ch09-lab/hello.sh` work?
    
3. Why is `755` common for executable scripts?
    
4. Would `777` provide any useful benefit here?
    
5. What does `x` mean on an ordinary file?
    

## Core lesson

A script does not need to be executable when passed as data to an interpreter:

```bash
bash script.sh
```

It does need execute permission when launched directly:

```bash
./script.sh
```

---

# Lab 5: Discover what `r`, `w`, and `x` mean on directories

## Goal

Stop treating directory permissions as if they behaved exactly like file permissions. For directories, `r` permits listing names, `w` permits creating, deleting, or renaming entries when combined with `x`, and `x` permits traversal. ([LinuxCommand](https://linuxcommand.org/lc3_lts0090.php?utm_source=chatgpt.com "Learning the shell - Lesson 9: Permissions"))

Create a directory and a readable file:

```bash
sudo -u alice mkdir /srv/ch09-lab/dirmodes
sudo -u alice bash -c 'echo "known secret" > /srv/ch09-lab/dirmodes/known.txt'
sudo chmod 644 /srv/ch09-lab/dirmodes/known.txt
```

## Experiment A: Read without traverse

Give Alice read permission on the directory but no execute permission:

```bash
sudo chmod 400 /srv/ch09-lab/dirmodes
sudo -u alice ls /srv/ch09-lab/dirmodes
sudo -u alice cat /srv/ch09-lab/dirmodes/known.txt
```

Observe:

- Alice can list the filename.
    
- Alice cannot open the file.
    

## Experiment B: Traverse without list

Give Alice execute permission but no read permission:

```bash
sudo chmod 100 /srv/ch09-lab/dirmodes
sudo -u alice ls /srv/ch09-lab/dirmodes
sudo -u alice cat /srv/ch09-lab/dirmodes/known.txt
```

Observe:

- Alice cannot list the directory normally.
    
- Alice can open `known.txt` because she already knows its exact name.
    

## Experiment C: Write and traverse without list

Give Alice write and execute permission but no read permission:

```bash
sudo chmod 300 /srv/ch09-lab/dirmodes
sudo -u alice touch /srv/ch09-lab/dirmodes/new.txt
sudo -u alice rm /srv/ch09-lab/dirmodes/known.txt
sudo -u alice ls /srv/ch09-lab/dirmodes
```

Observe:

- Alice can create and delete entries.
    
- Alice still cannot list the directory normally.
    

Restore a normal mode:

```bash
sudo chmod 750 /srv/ch09-lab/dirmodes
```

## Questions

1. Why did `cat known.txt` fail with directory mode `400`?
    
2. Why did it work with directory mode `100`?
    
3. Which directory permission is required to enter or traverse a directory?
    
4. Why is directory-write permission potentially more powerful than beginners expect?
    
5. Why might a web server return `403 Forbidden` even if `index.html` itself is readable?
    

## Core lesson

For a directory:

```text
r = list names
w = create, delete, or rename entries
x = enter or traverse
```

The `x` permission is essential.

---

# Lab 6: Follow permissions through a path

## Goal

Understand that every parent directory matters.

Create a nested path:

```bash
sudo -u alice mkdir -p /srv/ch09-lab/site/public
sudo -u alice bash -c 'echo "<h1>Hello</h1>" > /srv/ch09-lab/site/public/index.html'

sudo chmod 755 /srv/ch09-lab/site
sudo chmod 755 /srv/ch09-lab/site/public
sudo chmod 644 /srv/ch09-lab/site/public/index.html
```

Confirm Charlie can read the file:

```bash
sudo -u charlie cat /srv/ch09-lab/site/public/index.html
```

Remove traverse permission for others from one parent directory:

```bash
sudo chmod 750 /srv/ch09-lab/site
sudo -u charlie cat /srv/ch09-lab/site/public/index.html
```

Inspect the complete path:

```bash
namei -l /srv/ch09-lab/site/public/index.html
```

Restore access:

```bash
sudo chmod 755 /srv/ch09-lab/site
```

## Questions

1. Why did Charlie lose access even though `index.html` remained `644`?
    
2. Which parent directory blocked access?
    
3. Why is checking only the final file insufficient?
    
4. Which command makes path troubleshooting easier?
    
5. How does this relate to troubleshooting an Nginx `403` error?
    

## Core lesson

To reach a file, a process needs traverse permission on every parent directory in the path.

---

# Lab 7: Discover how `umask` affects new files and directories

## Goal

Observe default permission filtering. A `umask` removes permission bits from newly created files and directories. ([man7.org](https://man7.org/linux/man-pages/man2/umask.2.html?utm_source=chatgpt.com "umask(2) - Linux manual page"))

Create a controlled workspace as Alice:

```bash
sudo -u alice mkdir -p /srv/ch09-lab/umask-test
```

## Experiment A: Common default mask

```bash
sudo -u alice bash -c '
  cd /srv/ch09-lab/umask-test
  umask 0022
  touch file-022
  mkdir dir-022
  ls -ld file-022 dir-022
'
```

Expected modes:

```text
-rw-r--r-- file-022
drwxr-xr-x dir-022
```

## Experiment B: Private-by-default mask

```bash
sudo -u alice bash -c '
  cd /srv/ch09-lab/umask-test
  umask 0077
  touch file-077
  mkdir dir-077
  ls -ld file-077 dir-077
'
```

Expected modes:

```text
-rw------- file-077
drwx------ dir-077
```

## Experiment C: Group-friendly mask

```bash
sudo -u alice bash -c '
  cd /srv/ch09-lab/umask-test
  umask 0002
  touch file-002
  mkdir dir-002
  ls -ld file-002 dir-002
'
```

Expected modes:

```text
-rw-rw-r-- file-002
drwxrwxr-x dir-002
```

## Questions

1. Why does `umask 0022` produce `644` for a new file?
    
2. Why does the same mask produce `755` for a new directory?
    
3. Why do newly created ordinary files normally lack execute permission?
    
4. Does `umask` change files that already exist?
    
5. When might `0077` be appropriate?
    

## Core lesson

Think of `umask` as a filter:

```text
new ordinary file: start from 666, then remove masked bits
new directory:     start from 777, then remove masked bits
```

---

# Lab 8: Change ownership with `chown` and `chgrp`

## Goal

Separate mode permissions from ownership. GNU `chown` changes the user owner, the group owner, or both. ([man7.org](https://man7.org/linux/man-pages/man1/chown.1.html?utm_source=chatgpt.com "chown(1) - Linux manual page"))

Create a file owned by Alice:

```bash
sudo -u alice bash -c 'echo "ownership experiment" > /srv/ch09-lab/ownership.txt'
ls -l /srv/ch09-lab/ownership.txt
```

Change the owner:

```bash
sudo chown bob /srv/ch09-lab/ownership.txt
ls -l /srv/ch09-lab/ownership.txt
```

Change the group:

```bash
sudo chgrp project /srv/ch09-lab/ownership.txt
ls -l /srv/ch09-lab/ownership.txt
```

Change owner and group together:

```bash
sudo chown alice:project /srv/ch09-lab/ownership.txt
ls -l /srv/ch09-lab/ownership.txt
```

Use the alternate syntax for changing only the group:

```bash
sudo chown :root /srv/ch09-lab/ownership.txt
ls -l /srv/ch09-lab/ownership.txt

sudo chown :project /srv/ch09-lab/ownership.txt
ls -l /srv/ch09-lab/ownership.txt
```

## Questions

1. What does `chown bob file` change?
    
2. What does `chgrp project file` change?
    
3. What does `chown alice:project file` change?
    
4. What does `chown :project file` change?
    
5. Why are `chmod` and `chown` not interchangeable?
    

## Core lesson

These solve different problems:

```text
chmod = change permission bits
chown = change owner, group, or both
chgrp = change group
```

---

# Lab 9: Build a shared project directory correctly

## Goal

Give Alice and Bob collaborative access without granting Charlie write access.

Create a project directory:

```bash
sudo mkdir /srv/ch09-lab/shared-project
sudo chown alice:project /srv/ch09-lab/shared-project
sudo chmod 770 /srv/ch09-lab/shared-project
ls -ld /srv/ch09-lab/shared-project
```

Have Alice create a file:

```bash
sudo -u alice bash -c 'echo "Alice created this" > /srv/ch09-lab/shared-project/notes.txt'
ls -l /srv/ch09-lab/shared-project
```

Try to edit it as Bob:

```bash
sudo -u bob bash -c 'echo "Bob edited this" >> /srv/ch09-lab/shared-project/notes.txt'
```

Inspect the failure:

```bash
ls -l /srv/ch09-lab/shared-project/notes.txt
```

Alice’s default `umask` probably produced a file that Bob cannot edit.

Change the file mode:

```bash
sudo chmod 660 /srv/ch09-lab/shared-project/notes.txt
sudo -u bob bash -c 'echo "Bob edited this" >> /srv/ch09-lab/shared-project/notes.txt'
cat /srv/ch09-lab/shared-project/notes.txt
```

Confirm Charlie is blocked:

```bash
sudo -u charlie cat /srv/ch09-lab/shared-project/notes.txt
sudo -u charlie touch /srv/ch09-lab/shared-project/charlie.txt
```

## Questions

1. Why could Bob enter the directory?
    
2. Why could Bob not initially modify `notes.txt`?
    
3. Why did `chmod 660 notes.txt` solve the file-editing problem?
    
4. Why can Charlie not create a file in the directory?
    
5. Why is `chmod -R 777 shared-project` a bad solution?
    

## Core lesson

Directory access and file access are related but separate. A user may be allowed to enter a directory without being allowed to modify every file inside it.

---

# Lab 10: Compare `su`, `sudo`, and the current identity

## Goal

Observe which identity executes a command.

Run:

```bash
whoami
id
```

Run one command with elevated privileges:

```bash
sudo whoami
sudo id
```

Run a command as Alice:

```bash
sudo -u alice whoami
sudo -u alice id
```

Start a login shell as Alice:

```bash
sudo su - alice
whoami
id
pwd
exit
```

Start a root shell:

```bash
sudo -i
whoami
id
pwd
exit
```

Check that you returned to the original account:

```bash
whoami
```

## Questions

1. What does `sudo whoami` print?
    
2. What does `sudo -u alice whoami` print?
    
3. Why did `pwd` change after `sudo su - alice`?
    
4. Why should you exit a root shell promptly?
    
5. When is `sudo command` safer than working continuously as `root`?
    

## Core lesson

Prefer narrowly scoped privilege elevation:

```bash
sudo command
```

Use an interactive root shell only when it is genuinely necessary.

---

# Lab 11: Practice `passwd` without damaging a real account

## Goal

See how passwords belong to user accounts and how password changes work.

Assign Charlie a temporary password:

```bash
sudo passwd charlie
```

Choose a disposable password suitable only for this practice VM.

Switch to Charlie:

```bash
su - charlie
```

Verify the identity:

```bash
whoami
id
pwd
```

Change Charlie’s password while logged in as Charlie:

```bash
passwd
```

Exit:

```bash
exit
```

Inspect password status:

```bash
sudo passwd -S charlie
```

Lock Charlie’s password when finished:

```bash
sudo passwd -l charlie
sudo passwd -S charlie
```

## Questions

1. Why could the administrator initially set Charlie’s password?
    
2. Why did Charlie need the current password before changing it?
    
3. What does `passwd -S charlie` report?
    
4. What does `passwd -l charlie` do?
    
5. Why should test accounts be locked or removed afterward?
    

## Core lesson

An administrator can manage another account’s password. A regular user changes their own password with:

```bash
passwd
```

---

# Lab 12: Troubleshoot a simulated deployment failure

## Goal

Diagnose a realistic permission problem instead of blindly using `chmod 777`.

Create a deployment path:

```bash
sudo mkdir -p /srv/ch09-lab/site1/public
sudo chown root:project /srv/ch09-lab/site1
sudo chown root:project /srv/ch09-lab/site1/public

sudo chmod 750 /srv/ch09-lab/site1
sudo chmod 755 /srv/ch09-lab/site1/public
```

Attempt a deployment as Alice:

```bash
sudo -u alice bash -c 'echo "<h1>Deployed</h1>" > /srv/ch09-lab/site1/public/index.html'
```

Expected result:

```text
Permission denied
```

Inspect the path:

```bash
ls -ld /srv /srv/ch09-lab /srv/ch09-lab/site1 /srv/ch09-lab/site1/public
namei -l /srv/ch09-lab/site1/public/index.html
```

Determine the missing permission.

Fix only the actual problem:

```bash
sudo chmod 775 /srv/ch09-lab/site1/public
```

Try again:

```bash
sudo -u alice bash -c 'echo "<h1>Deployed</h1>" > /srv/ch09-lab/site1/public/index.html'
cat /srv/ch09-lab/site1/public/index.html
```

Confirm that Charlie still cannot deploy:

```bash
sudo -u charlie bash -c 'echo "unauthorized" > /srv/ch09-lab/site1/public/charlie.txt'
```

## Questions

1. Why could Alice traverse `site1`?
    
2. Why could Alice not initially create `index.html`?
    
3. Why did changing `public` from `755` to `775` solve the problem?
    
4. Why did Charlie remain blocked?
    
5. Why would this be the wrong response?
    
    ```bash
    sudo chmod -R 777 /srv/ch09-lab/site1
    ```
    

## Core lesson

A good administrator does not respond to `Permission denied` by granting everyone everything. Inspect the path, identify the relevant identity, and change only the required permission.

---

# Final challenge: Repair the broken permissions

Do not provide commands to the student. Give him only this scenario.

Create the broken environment first:

```bash
sudo rm -rf /srv/ch09-lab/final-challenge
sudo mkdir -p /srv/ch09-lab/final-challenge/public
sudo chown root:project /srv/ch09-lab/final-challenge
sudo chown root:project /srv/ch09-lab/final-challenge/public
sudo chmod 740 /srv/ch09-lab/final-challenge
sudo chmod 755 /srv/ch09-lab/final-challenge/public
```

Give him this requirement:

> Alice and Bob must be able to enter `final-challenge`, upload files into `public`, and modify the uploaded files. Charlie must not be able to enter `final-challenge`. Do not use `777`.

He should inspect the directory tree, choose appropriate owner and group settings, choose suitable modes, create a test file as Alice, edit it as Bob, and prove that Charlie is blocked.

A reasonable solution will involve:

```text
project group membership
directory traversal permissions
directory write permissions
file group-write permissions
verification as alice, bob, and charlie
```

There is more than one defensible implementation. The test results matter more than copying one exact command sequence.

---

# Cleanup

After completing the labs:

```bash
sudo rm -rf /srv/ch09-lab

sudo userdel -r alice
sudo userdel -r bob
sudo userdel -r charlie

sudo groupdel project
```

# Recommended order

|Session|Labs|
|---|---|
|First session|1–4|
|Second session|5–7|
|Third session|8–10|
|Fourth session|11–12|
|Final review|Final challenge|

Labs 5, 6, 9, and 12 are the most important. They force the student to reason about access instead of treating permissions as a table of `chmod` numbers.