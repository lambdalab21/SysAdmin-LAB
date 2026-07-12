#Book/The-Linux-Command-Line #Author/Shotts 
#text-processing #regex
# TLCL Chapter 20 Practice Using Chapter 19 Regular Expressions

These exercises combine:

```text
Chapter 19: regular expressions
Chapter 20: text processing
```

Main goal:

```text
Do not type pipelines mindlessly.
Use regex as a disciplined filter, then use text-processing commands to answer a question.
```

Core thinking pattern:

```text
1. What question am I answering?
2. Which lines should match?
3. What regex describes those lines?
4. What false positives might appear?
5. What false negatives might I miss?
6. What text-processing command comes next?
7. Did I inspect each stage before trusting the final answer?
```

Pipeline discipline:

```text
Do not build a five-command pipeline in one shot.
Build one stage, inspect it, then add the next stage.
```

Feynman rule:

```text
Before running the command, explain it as if teaching a ten-year-old.
After running it, explain why the output appeared.
```

One-time setup:

```bash
mkdir -p ~/tlcl-ch20-regex-practice/{logs,config,packages,passwd,output,reports}
cd ~/tlcl-ch20-regex-practice

cat > logs/app.log <<'EOF'
INFO 2026-06-01 08:01:02 app01 started service=web
ERROR 2026-06-01 08:02:17 app01 failed-login user=alice ip=192.168.10.25
INFO 2026-06-01 08:03:44 app01 request path=/index.html status=200
WARN 2026-06-01 08:04:01 db01 disk-space-low mount=/var percent=91
ERROR 2026-06-01 08:05:31 db01 timeout service=mysql seconds=30
ERROR 2026-06-02 09:10:11 app01 failed-login user=bob ip=192.168.10.31
INFO 2026-06-02 09:11:12 app01 request path=/login status=200
INFO 2026-06-02 09:12:13 app01 request path=/admin status=403
DEBUG 2026-06-02 09:13:14 app02 cache-warmed entries=128
error 2026-06-02 09:14:15 app02 lowercase-error-example
WARN 2026-06-02 09:15:16 app02 memory-high percent=82
ERROR 2026-06-02 09:16:17 app02 failed-login user=charlie ip=192.168.10.44
EOF

cat > config/sshd_config_sample <<'EOF'
#Port 22
Port 22
PermitRootLogin no
PasswordAuthentication yes
#PasswordAuthentication no
PubkeyAuthentication yes
X11Forwarding no
AllowUsers alice bob deploy
EOF

cat > config/nginx_site_sample.conf <<'EOF'
server {
    listen 80;
    server_name app01.local;

    root /var/www/app;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /admin {
        allow 192.168.10.0/24;
        deny all;
    }
}
EOF

cat > packages/apt_list_sample.txt <<'EOF'
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

cat > passwd/passwd_sample <<'EOF'
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
alice:x:1001:1001:Alice:/home/alice:/bin/bash
bob:x:1002:1002:Bob:/home/bob:/bin/zsh
charlie:x:1003:1003:Charlie:/home/charlie:/usr/sbin/nologin
deploy:x:1004:1004:Deploy User:/srv/deploy:/bin/bash
mysql:x:112:118:MySQL Server:/nonexistent:/usr/sbin/nologin
EOF

cat > output/ps_sample.txt <<'EOF'
USER         PID %CPU %MEM COMMAND
root           1  0.0  0.3 systemd
root         702  0.0  0.2 sshd
www-data    1041  0.3  1.1 nginx
www-data    1042  0.2  1.0 nginx
mysql       1100  1.4  8.5 mysqld
alice       2110  0.0  0.1 bash
alice       2120  0.0  0.2 vim
EOF

cat > output/ss_sample.txt <<'EOF'
Netid State  Recv-Q Send-Q Local Address:Port Peer Address:Port Process
tcp   LISTEN 0      128    0.0.0.0:22         0.0.0.0:*     users:(("sshd",pid=702,fd=3))
tcp   LISTEN 0      511    0.0.0.0:80         0.0.0.0:*     users:(("nginx",pid=1041,fd=6))
tcp   LISTEN 0      80     127.0.0.1:3306     0.0.0.0:*     users:(("mysqld",pid=1100,fd=20))
udp   UNCONN 0      0      127.0.0.53:53      0.0.0.0:*     users:(("systemd-resolve",pid=530,fd=12))
EOF
```

---
# Day 7: Final Mixed Lab — Regex + Text Processing

## Purpose

This is the final check.

He should not look at answers first.

For every task, use this structure:

```text
Question:
English matching rule:
Regex:
Pipeline stages:
Command:
Output:
False positives checked:
False negatives checked:
Explain-back:
```

---

# Lab A: Log investigation

Answer these using `logs/app.log`.

1. Count lines beginning with uppercase ERROR.
2. Count lines beginning with ERROR or WARN.
3. List users with failed-login events.
4. Count serious lines by host.
5. List IP-like values in failed-login lines.
6. Count HTTP status codes.

Expected command patterns:

```bash
grep '^ERROR' logs/app.log | wc -l
grep -E '^(ERROR|WARN)' logs/app.log | wc -l
grep 'failed-login' logs/app.log | cut -d ' ' -f6 | cut -d '=' -f2 | sort | uniq
grep -E '^(ERROR|WARN)' logs/app.log | cut -d ' ' -f4 | sort | uniq -c
grep 'failed-login' logs/app.log | grep -Eo '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+'
grep 'request' logs/app.log | grep -Eo 'status=[0-9]{3}' | cut -d '=' -f2 | sort | uniq -c
```

Note:

```text
grep -o prints only the matching part. If unavailable or not covered yet, use cut/sed alternatives.
```

---

# Lab B: Config inspection

Answer these using `config/sshd_config_sample` and `config/nginx_site_sample.conf`.

1. Show active SSH settings only.
2. Show both active and commented PasswordAuthentication settings.
3. Show the active PasswordAuthentication setting only.
4. Find indented Nginx `listen` directive.
5. Extract Nginx directive names.

Expected command patterns:

```bash
grep -v '^#' config/sshd_config_sample | grep '[[:alpha:]]'
grep -E '^#?PasswordAuthentication' config/sshd_config_sample
grep '^PasswordAuthentication' config/sshd_config_sample
grep -E '^[[:space:]]*listen' config/nginx_site_sample.conf
grep -E '^[[:space:]]*[a-z_]+ ' config/nginx_site_sample.conf | sed 's/^[[:space:]]*//' | cut -d ' ' -f1 | sort | uniq
```

---

# Lab C: Package and passwd output

Answer these using `packages/apt_list_sample.txt` and `passwd/passwd_sample`.

1. Count installed packages.
2. List installed package names.
3. Match packages named bash, grep, git, or vim.
4. List users with `/bin/bash` shell.
5. Summarize shells.

Expected command patterns:

```bash
grep '\[installed\]' packages/apt_list_sample.txt | wc -l
grep '\[installed\]' packages/apt_list_sample.txt | cut -d '/' -f1 | sort
grep -E '^(bash|grep|git|vim)/' packages/apt_list_sample.txt
grep '/bin/bash$' passwd/passwd_sample | cut -d ':' -f1
cut -d ':' -f7 passwd/passwd_sample | sort | uniq -c
```

---

# Lab D: Write a mini report

Create:

```bash
reports/final-summary.txt
```

It should include:

```text
number of ERROR lines
number of ERROR/WARN lines
failed-login users
installed package count
bash-shell users
listening TCP address:port values from ss sample
```

Suggested pattern:

```bash
{
  printf 'Final Text Processing Report
'
  printf '============================

'

  printf 'ERROR lines: '
  grep '^ERROR' logs/app.log | wc -l

  printf 'ERROR/WARN lines: '
  grep -E '^(ERROR|WARN)' logs/app.log | wc -l

  printf '
Failed-login users:
'
  grep 'failed-login' logs/app.log | cut -d ' ' -f6 | cut -d '=' -f2 | sort | uniq

  printf '
Installed package count: '
  grep '\[installed\]' packages/apt_list_sample.txt | wc -l

  printf '
Bash-shell users:
'
  grep '/bin/bash$' passwd/passwd_sample | cut -d ':' -f1

  printf '
Listening TCP local addresses:
'
  grep '^tcp' output/ss_sample.txt | tr -s ' ' | cut -d ' ' -f5
} > reports/final-summary.txt

cat reports/final-summary.txt
```

---

# Final oral exam

Explain these without notes:

1. Why does `grep -E '^(ERROR|WARN)'` use both `^` and parentheses?
2. Why is `grep '\[installed\]'` different from `grep '[installed]'`?
3. Why does `sort | uniq -c` usually appear in that order?
4. Why is `cut` dangerous if you have not inspected fields?
5. Why is `sed` powerful but sometimes less readable than `cut`?
6. Why is report generation better than copying output manually?
7. How do you check false positives and false negatives?

---

# End standard

He understands Chapter 20 practice with Chapter 19 regex only if he can say:

```text
I can write the English rule before the regex.
I can explain the regex symbols.
I can build a pipeline one stage at a time.
I can inspect intermediate output.
I can summarize with sort and uniq.
I can avoid trusting pretty output before verifying it.
```
