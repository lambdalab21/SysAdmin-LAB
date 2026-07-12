#Book/The-Linux-Command-Line Author/Shotts 
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

# Day 1: `grep` and Literal Matching

## Read these Chapter 19 sections

Read the opening of Chapter 19 and the section introducing regular expressions, `grep`, literal matching, and case sensitivity.

Do not rush to symbols yet. The first job is to understand what `grep` does.

## Feynman analogy

Imagine a stack of cards. Each card has one line of text. `grep` checks each card and asks: “Does this card match my rule?” If yes, it shows the whole card.

Important: `grep` usually prints matching lines, not only the matching word.

## Before touching the keyboard

Answer:

1. What does `grep` print?
2. Does `grep ERROR app.log` search words, lines, or files?
3. What does case-sensitive mean?
4. What do you expect `grep error app.log` to miss?

## Practice

```bash
cd ~/tlcl-ch19-regex
cp app.log day1/
cd day1

grep ERROR app.log
grep error app.log
grep -i error app.log
grep INFO app.log
grep app01 app.log
grep failed-login app.log
```

After each command, say:

```text
My pattern was...
The matching lines were...
This did or did not match because...
```

## Discipline drill

Before running this, predict what will match:

```bash
grep app app.log
```

Then test uppercase/lowercase behavior:

```bash
printf 'Application started\napplication stopped\n' > case-test.txt
grep app case-test.txt
grep -i app case-test.txt
```

## Explain to a ten-year-old

Explain `grep -i error app.log`:

```text
grep is like a card checker.
-i tells it...
The word error matches...
The output is...
```

## Checkpoint

He understands Day 1 only if he can answer: what `grep` prints, what `-i` does, why `grep error` misses `ERROR`, and the difference between a matching line and a matching word.
