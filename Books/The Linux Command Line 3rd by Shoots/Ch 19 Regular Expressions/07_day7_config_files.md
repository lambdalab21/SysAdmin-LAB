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

# Day 7: Apply Regex to Configuration Files

## Purpose

Configuration files are one of the most important places to use regex carefully. Practice comments, active settings, disabled settings, directives, and safe inspection before editing.

## Practice

```bash
cd ~/tlcl-ch19-regex
cp sshd_config_sample nginx_site_sample.conf day7/
cd day7
cat sshd_config_sample
cat nginx_site_sample.conf
```

## Comments versus active lines

```bash
grep '^#' sshd_config_sample
grep -v '^#' sshd_config_sample
grep -v '^#' sshd_config_sample | grep '[[:alpha:]]'
```

Question: Why might `grep -v '^#'` still show blank lines? It still shows  blank lines because they do not begin with #. 

## Find specific directives

```bash
grep '^PermitRootLogin' sshd_config_sample
grep '^PasswordAuthentication' sshd_config_sample
grep '^#PasswordAuthentication' sshd_config_sample
grep -E '^#?PasswordAuthentication' sshd_config_sample
```

Question: What does `#?` mean in extended regex? #? in extended regex means "zero or one # character". 

## Nginx-style config

```bash
grep 'listen' nginx_site_sample.conf
grep 'server_name' nginx_site_sample.conf
grep 'root' nginx_site_sample.conf
grep -E '^[[:space:]]*listen' nginx_site_sample.conf
```

Question: Why use `^[[:space:]]*listen` instead of `^listen`? It's used instead of ^listen because NGinx directives can be indented with spaces or tabs. 
