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

# Day 4: Extended Regex, Alternation, and Grouping

## Read these Chapter 19 sections

Read the sections covering extended regular expressions: `grep -E`, `+`, `?`, `{}`, `|`, and `()`.

## Feynman analogy

Extended regex lets you ask more natural questions: “one or more digits,” “optional letter,” “ERROR or WARN,” and “group these choices together.” Grouping is like parentheses in math.

## Before touching the keyboard

Answer what `grep -E`, `+`, `?`, `|`, and parentheses do.

## Practice

```bash
cd ~/tlcl-ch19-regex
cp words.txt app.log day4/
cd day4

cat > extended.txt <<'EOF'
a
ab
abb
abbb
color
colour
gray
grey
ERROR failed
WARN low disk
INFO started
123
abc123
EOF
```

## `+` means one or more

```bash
grep -E 'ab+' extended.txt
grep 'ab*' extended.txt
grep -E 'ab+' extended.txt
```

Say aloud: `*` means zero or more; `+` means one or more.

## `?`, `{}`, `|`, and grouping

```bash
grep -E 'colou?r' extended.txt
grep -E 'gr(a|e)y' extended.txt
grep -E 'ab{2}' extended.txt
grep -E 'ab{2,3}' extended.txt
grep -E '[[:digit:]]{3}' extended.txt
```

## Logs

```bash
grep -E 'ERROR|WARN' app.log
grep -E '^(ERROR|WARN)' app.log
grep -E '(alice|bob)' app.log
```

Questions:

1. What is stricter: `ERROR|WARN` or `^(ERROR|WARN)`?
2. Why do parentheses matter?

## Checkpoint

Explain `grep -E`, `*` vs `+`, `?`, `|`, and why `^(ERROR|WARN)` does not match INFO lines.
