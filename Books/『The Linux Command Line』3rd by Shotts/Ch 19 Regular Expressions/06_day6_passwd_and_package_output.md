#Book/The-Linux-Command-Line #Author/Shotts 
#regex
# The Linux Command Line, Chapter 19: Regular Expressions

Use these files as a multi-day study plan for Author/Shotts's Chapter 19.

Main discipline:

```text
Do not treat regex as magic symbols.
A regular expression is a small rule for matching text.
```

Before every command, answer:

```text
1. What lines do I expect to match?
2. Which part of the regex creates that match?
3. Could the shell change my pattern before grep sees it?
4. Did the output prove my prediction?
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch19-regex/{day1,day2,day3,day4,day5,day6,day7,day8}
cd ~/tlcl-ch19-regex

cat > app.log <<'EOF'
INFO 2026-06-01 app01 started
ERROR 2026-06-01 app01 failed-login alice
INFO 2026-06-01 app01 request /index.html
WARN 2026-06-01 db01 disk-space-low
ERROR 2026-06-02 db01 timeout
ERROR 2026-06-02 app01 failed-login bob
INFO 2026-06-02 app01 stopped
DEBUG 2026-06-03 app02 cache-warmed
error 2026-06-03 app02 lowercase-error-example
EOF

cat > words.txt <<'EOF'
cat
cot
cut
coat
cost
cast
cart
dog
dot
dig
gray
grey
gravy
abc123
abc
123
EOF

cat > packages.txt <<'EOF'
bash/stable,now 5.2 amd64 [installed]
coreutils/stable,now 9.1 amd64 [installed]
curl/stable 8.5 amd64
git/stable,now 2.43 amd64 [installed]
grep/stable,now 3.11 amd64 [installed]
nginx/stable 1.24 amd64
openssh-server/stable,now 9.6 amd64 [installed]
python3/stable,now 3.12 amd64 [installed]
vim/stable,now 9.1 amd64 [installed]
zsh/stable 5.9 amd64
EOF

cat > sshd_config_sample <<'EOF'
#Port 22
PermitRootLogin no
PasswordAuthentication yes
#PasswordAuthentication no
PubkeyAuthentication yes
X11Forwarding no
AllowUsers alice bob deploy
EOF

cat > nginx_site_sample.conf <<'EOF'
server {
    listen 80;
    server_name app01.local;
    root /var/www/app;
    index index.html;
    location / {
        try_files $uri $uri/ =404;
    }
}
EOF
```

---

# Day 6: Apply Regex to `/etc/passwd` and Package Output

## Purpose

This connects regex to real Linux output: `/etc/passwd`, `apt list`, package lists, installed markers, shell paths, and usernames.

## Feynman analogy

Real system output is like a crowded bulletin board. Regex asks: Which notices start with this word? Which contain this marker? Which end with this shell?

## Discipline rule

Before every command, write:

```text
I expect this to match...
I expect this not to match...
The risky part of this pattern is...
```

## `/etc/passwd` practice

```bash
grep '^root' /etc/passwd
grep 'bash$' /etc/passwd
grep -E 'bash$|sh$' /etc/passwd
grep 'nologin$' /etc/passwd
grep -E '^[a-z_][a-z0-9_-]*:' /etc/passwd | head
```

Questions:

1. What does `^root` mean?
2. What does `bash$` mean?
3. Why does the username regex end with `:`?
4. Why inspect with `head`?

## Package output practice

On Debian/Ubuntu:

```bash
apt list --installed 2>/dev/null | head
apt list --installed 2>/dev/null | grep '^bash/'
apt list --installed 2>/dev/null | grep -E '^(bash|grep|coreutils)/'
```

Otherwise use sample data:

```bash
cd ~/tlcl-ch19-regex
cp packages.txt day6/
cd day6
grep '^bash/' packages.txt
grep -E '^(bash|grep|coreutils)/' packages.txt
grep '\[installed\]' packages.txt
grep -E '^(git|vim|zsh)/' packages.txt
```

## Bracket literal trap

```bash
grep '[installed]' packages.txt
grep '\[installed\]' packages.txt
```

Explain why they differ. `[installed]` is a character class; `\[installed\]` matches literal brackets.

## Combine with Chapter 20

```bash
grep '\[installed\]' packages.txt | cut -d '/' -f1 | sort
```

Say aloud: `grep` selected rows, `cut` selected a field, `sort` ordered the result.
