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
# Day 5: Regex with `/etc/passwd` and Package-Style Output

## Chapter 20 tools used

```text
grep
cut
sort
uniq
wc
```

## Chapter 19 regex used

```text
^
$
[]
[[:digit:]]
[[:alpha:]]
+
|
()
\[ literal bracket
```

---

## Feynman analogy

System files and command output are structured text.

Regex chooses the rows.

`cut`, `sort`, `uniq`, and `wc` turn those rows into answers.

---

## Exercise 1: `/etc/passwd` shell summary

Use sample first:

```bash
cat passwd/passwd_sample
```

Extract login shells:

```bash
cut -d ':' -f7 passwd/passwd_sample
```

Summarize:

```bash
cut -d ':' -f7 passwd/passwd_sample | sort | uniq -c
```

Now use regex first:

```bash
grep '/bin/bash$' passwd/passwd_sample
```

Question:

```text
Which users have /bin/bash as their shell?
```

Pipeline:

```bash
grep '/bin/bash$' passwd/passwd_sample | cut -d ':' -f1
```

---

## Exercise 2: System accounts versus normal-looking users

Question:

```text
Which entries have UID numbers in the 1000 range?
```

Build:

```bash
grep -E '^[^:]+:x:100[0-9]:' passwd/passwd_sample
```

Then extract usernames and UIDs:

```bash
grep -E '^[^:]+:x:100[0-9]:' passwd/passwd_sample | cut -d ':' -f1,3
```

Explain:

```text
^[^:]+ matches the username up to the first colon.
:x:100[0-9]: matches UID 1000 through 1009.
```

Discipline warning:

```text
This is practice data. Real UID policies differ by distribution.
```

---

## Exercise 3: Installed packages

Inspect:

```bash
cat packages/apt_list_sample.txt
```

Loose command:

```bash
grep 'installed' packages/apt_list_sample.txt
```

Precise literal bracket command:

```bash
grep '\[installed\]' packages/apt_list_sample.txt
```

Discipline question:

```text
Why not grep '[installed]'?
```

Expected answer:

```text
[installed] is a character class, not the literal word in brackets.
```

---

## Exercise 4: Extract installed package names

Build:

```bash
grep '\[installed\]' packages/apt_list_sample.txt
```

```bash
grep '\[installed\]' packages/apt_list_sample.txt | cut -d '/' -f1
```

```bash
grep '\[installed\]' packages/apt_list_sample.txt | cut -d '/' -f1 | sort
```

Count:

```bash
grep '\[installed\]' packages/apt_list_sample.txt | cut -d '/' -f1 | wc -l
```

---

## Exercise 5: Match selected package families

Question:

```text
Which lines are for bash, grep, git, or vim?
```

Run:

```bash
grep -E '^(bash|grep|git|vim)/' packages/apt_list_sample.txt
```

Explain:

```text
^ anchors the package name to the beginning.
(bash|grep|git|vim) means one of these names.
/ ensures the match is the package name before the suite field.
```

---

## End checkpoint

He understands this file only if he can answer:

1. Why is `/etc/passwd` good text-processing practice?
2. What does `cut -d ':' -f1` extract?
3. How do you match a literal `[`?
4. Why is `grep '[installed]'` wrong here?
5. How do regex and `cut` work together on package output?
