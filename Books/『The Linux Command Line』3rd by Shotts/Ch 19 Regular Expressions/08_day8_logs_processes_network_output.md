# The Linux Command Line, Chapter 19: Regular Expressions

Use these files as a multi-day study plan for Shotts's Chapter 19.

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

# Day 8: Apply Regex to Logs, Processes, and Network Output

## Purpose

Use regex with logs, process lists, network sockets, and system command output.

## Feynman analogy

Troubleshooting is finding clues in messy notes. Regex is the clue detector. A detective asks: What clue proves my idea? What clue disproves it? What false clue might fool me?

## Practice

```bash
cd ~/tlcl-ch19-regex
cp app.log day8/
cd day8
```

## Log levels

```bash
grep '^ERROR' app.log
grep -E '^(ERROR|WARN)' app.log
grep -E '^(INFO|DEBUG)' app.log
grep -i '^error' app.log
```

Questions: Which matches only uppercase ERROR at the beginning? Which also matches lowercase error? Which matches serious lines?

## Failed-login users

```bash
grep 'failed-login' app.log
grep -E 'failed-login (alice|bob)' app.log
grep -E 'failed-login [[:alpha:]]+$' app.log
```

Question: What does `[[:alpha:]]+$` mean at the end?

## Process output

```bash
ps aux | head
ps aux | grep bash
ps aux | grep -E 'bash|ssh'
ps aux | grep '[b]ash'
```

Question: Why can `grep bash` show the grep command itself?

## Network/socket output

```bash
ss -tuln | head
ss -tuln | grep ':22'
ss -tuln | grep -E ':22|:80|:443'
```

Important: a listening port does not prove the application works correctly.

## Combine regex with Chapter 20 pipelines

```bash
grep -E '^(ERROR|WARN)' app.log | wc -l
grep 'failed-login' app.log | grep -E '(alice|bob)'
grep -E '^ERROR' app.log | cut -d ' ' -f3 | sort | uniq -c
```

## Final test

For each command, explain what could go wrong: case sensitivity, false positives, missing anchors, grep matching itself, and mistaking a listening port for service correctness.
