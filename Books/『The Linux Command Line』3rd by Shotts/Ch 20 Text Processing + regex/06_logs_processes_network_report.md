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
# Day 6: Logs, Processes, Network Output, and Reports

## Chapter 20 tools used

```text
grep
cut
sort
uniq
wc
printf
sed
```

## Chapter 19 regex used

```text
^
$
[]
[[:digit:]]
[[:space:]]
+
|
()
```

---

## Feynman analogy

A sysadmin report is not a guess.

It is a short answer built from evidence.

```text
raw output → regex filter → field extraction → summary → report
```

---

## Exercise 1: Process list summary

Inspect:

```bash
cat output/ps_sample.txt
```

Find web/database processes:

```bash
grep -E '(nginx|mysqld)' output/ps_sample.txt
```

Extract command names:

```bash
grep -E '(nginx|mysqld)' output/ps_sample.txt | tr -s ' ' | cut -d ' ' -f6
```

Summarize:

```bash
grep -E '(nginx|mysqld)' output/ps_sample.txt | tr -s ' ' | cut -d ' ' -f6 | sort | uniq -c
```

Discipline question:

```text
Why did we use tr -s ' ' before cut?
```

Expected idea:

```text
ps output uses variable spacing. tr -s squeezes repeated spaces into one delimiter.
```

---

## Exercise 2: Listening port summary

Inspect:

```bash
cat output/ss_sample.txt
```

Find TCP listening sockets:

```bash
grep '^tcp' output/ss_sample.txt
```

Find common ports:

```bash
grep -E ':(22|80|3306)[[:space:]]' output/ss_sample.txt
```

Extract local address:port field:

```bash
grep '^tcp' output/ss_sample.txt | tr -s ' ' | cut -d ' ' -f5
```

Discipline question:

```text
Why is ss output harder to parse blindly than a simple CSV file?
```

Expected idea:

```text
Spacing and columns can vary. Inspect first.
```

---

## Exercise 3: Create a log report

Build the pieces first:

```bash
grep '^ERROR' logs/app.log | wc -l
```

```bash
grep -E '^(ERROR|WARN)' logs/app.log | wc -l
```

```bash
grep 'failed-login' logs/app.log | cut -d ' ' -f6 | cut -d '=' -f2 | sort | uniq
```

Now make a report:

```bash
{
  printf 'Log Report
'
  printf '==========

'
  printf 'ERROR lines: '
  grep '^ERROR' logs/app.log | wc -l
  printf 'ERROR or WARN lines: '
  grep -E '^(ERROR|WARN)' logs/app.log | wc -l
  printf '
Failed-login users:
'
  grep 'failed-login' logs/app.log | cut -d ' ' -f6 | cut -d '=' -f2 | sort | uniq
} > reports/log-report.txt

cat reports/log-report.txt
```

Explain:

```text
Fixed text came from printf.
Evidence came from pipelines.
The report is reproducible because commands generated it.
```

---

## Exercise 4: Verify report claims

For each report line, run the pipeline again manually.

Questions:

```text
Can I prove the ERROR count?
Can I prove the failed-login users?
Can I explain every stage?
```

If not, the report is not trustworthy yet.

---

## End checkpoint

He understands this file only if he can answer:

1. Why use `tr -s ' '` before `cut` on command output?
2. What does `grep '^tcp'` select?
3. Why should a report be reproducible?
4. What parts of the report are fixed text?
5. What parts are generated evidence?
