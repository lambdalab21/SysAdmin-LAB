#Book/The-Linux-Command-Line  #Author/Shotts 
#regex 
# The Linux Command Line, Chapter 19: Regular Expressions

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

# Day 5: Applied Review of Chapter 19

## Reading task

Skim Chapter 19 again. Look for examples involving `grep`, anchors, bracket expressions, POSIX classes, extended regex, and quoting.

## Practice

```bash
cd ~/tlcl-ch19-regex
cp app.log words.txt packages.txt day5/
cd day5
```

## Drill 1: Write the English rule first

```bash
grep '^INFO' app.log
grep 'bob$' app.log
grep '[[:digit:]]' app.log
grep -E '^(ERROR|WARN)' app.log
grep -E 'app0[12]' app.log
```

## Drill 2: Find false positives

```bash
grep 'error' app.log
grep -i 'error' app.log
grep -E 'ERROR|WARN' app.log
grep -E '^(ERROR|WARN)' app.log
```

## Package-style output

```bash
grep 'installed' packages.txt
grep '\[installed\]' packages.txt
grep '^git' packages.txt
grep -E '^(git|grep|vim)' packages.txt
grep -E 'amd64.*installed' packages.txt
```

Question: Why escape `[` and `]` in `\[installed\]`?

## Day 5 self-test

Write commands for: lines starting with ERROR, ending with bob, containing any digit, containing no digits, ERROR or WARN log lines, installed packages, and app01 or app02.

`grep '^ERROR' app.log`
`grep 'bob$' app.log`
`grep '[[:digit:]]' app.log`
`grep  -v '[[:digit:]]' app.log`
`grep -E '^(ERROR|WARN)' app.log`
`grep '\[installed\]' packages.txt
`grep  -E 'app0[12]' app.log` 
