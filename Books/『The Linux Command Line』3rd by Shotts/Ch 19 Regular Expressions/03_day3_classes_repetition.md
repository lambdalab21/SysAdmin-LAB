#Book/The-Linux-Command-Line #Author/Shotts 
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

# Day 3: POSIX Character Classes and Repetition

## Read these Chapter 19 sections

Read the sections covering POSIX character classes and repetition, especially `[[:alpha:]]`, `[[:digit:]]`, `[[:alnum:]]`, `[[:space:]]`, and `*`. Preview `+`, `?`, and `{}` for Day 4.

## Before touching the keyboard

Answer:

1. What does `[[:digit:]]` mean? Any digit between 0-9
2. Why does it use double brackets? Because POSIX character classes are written as `[[:name:]]`.
3. What does `*` mean in regex? Zero or more occurrences of the previous element. 
4. Why is regex `*` not the same as shell wildcard `*`? Shell wildcard * is a glob that matches any sequence of characters in filenames. 
5. What does `ab*` match? a, ab, abb, abbb, etc.

## Practice

```bash
cd ~/tlcl-ch19-regex
cp words.txt app.log day3/
cd day3

grep '[[:digit:]]' words.txt
grep '[[:alpha:]]' words.txt
grep '[[:alnum:]]' words.txt
grep '[[:space:]]' app.log
```

## Repetition with `*`

```bash
cat > repeat.txt <<'EOF'
a
ab
abb
abbb
ac
abc
b
EOF

grep 'ab*' repeat.txt
grep 'abb*' repeat.txt
grep 'abbb*' repeat.txt
```

Important: `b*` means zero or more b characters. So `ab*` can match `a`, `ab`, `abb`, and `abbb`.

## Trap drill: regex `*` is not shell `*`

Shell glob:

```bash
ls *.txt
```

Regex idea:

```bash
grep '.*txt' file
```

## Checkpoint

Explain `[[:digit:]]`, `[[:space:]]`, regex `*`, why `ab*` matches `a`, and the difference between shell wildcard `*` and regex `*`.

* [[:digit:]] = any digit, 0-9. 
* [[:space:]] = any whitespace character. 
* Regex * = zero or more repetitions of the previous character or group. 
* ab* matches a because * allows zero bs after the a
* Difference between shell * and regex *:
	* Shell * is a wildcard that matches any characters in file names.
	* Regex * is a quantifier meaning "zero or more of the preceding pattern".