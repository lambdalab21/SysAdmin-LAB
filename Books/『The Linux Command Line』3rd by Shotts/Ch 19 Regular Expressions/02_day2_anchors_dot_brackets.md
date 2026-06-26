# The Linux Command Line, Chapter 19: Regular Expressions

Use these files as a multi-day study plan for Shotts's Chapter 19.

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

# Day 2: Anchors, Dot, and Bracket Expressions

## Read these Chapter 19 sections

Read the sections covering `.`, `^`, `$`, `[]`, `[^]`, and ranges such as `[0-9]` and `[a-z]`.

## Before touching the keyboard

Answer:

1. What does `^` mean at the beginning of a regex? Matches the start of a line
2. What does `$` mean at the end? Matches the end of a line. 
3. What does `.` match? Matches any single characters. 
4. What does `[aeiou]` mean? Matches any vowel. 
5. What does `[^0-9]` mean? Matches any character that is not a digit. 

## Practice

```bash
cd ~/tlcl-ch19-regex
cp words.txt app.log day2/
cd day2

grep '^ERROR' app.log
grep 'bob$' app.log
grep '^INFO' app.log
grep '^error' app.log
grep -i '^error' app.log
```
## Bracket practice

```bash
grep 'c[ao]t' words.txt
grep 'c[aou]t' words.txt
grep '[0-9]' words.txt
grep '^[0-9]' words.txt
grep '[^0-9]' words.txt
grep -v '[0-9]' words.txt
```

Important: `[^0-9]` means one character that is not a digit. It does not mean “a line with no digits.” Use `grep -v '[0-9]'` for lines with no digits.
## Checkpoint

Explain `^ERROR`, `bob$`, `c.t`, `[0-9]`, and the difference between `grep '[^0-9]'` and `grep -v '[0-9]'`.

* ^ERROR Matches lines that start with "ERROR".
* bob$ matches lines that end with "bob".
* c.t matches any three-character sequence starting with "c", ending with "t", with any character in between. Like cat, cot, and cut.
* [0-9] matches any single digit from 0 through 9.

* grep '[^0-9]' matches lines that contain at least one non-digit character.
* grep -v '[0-9]' matches lines that contain no digits at all. 