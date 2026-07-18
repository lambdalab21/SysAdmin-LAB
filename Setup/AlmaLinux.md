#linux #setup  #alma-linux

[setup](https://chatgpt.com/s/t_6a12f551e2c88191a6ec77a167b1bc15)


Now make the AlmaLinux machine the **app server**, not just “another Linux install.”

Assume:

```text
app01 = AlmaLinux application server
db01  = Ubuntu database server
```

Target goal:

```text
Browser → app01/nginx → Clojure app → db01/MariaDB
```

## 1. Update AlmaLinux

On the AlmaLinux machine:

```bash
sudo dnf update -y
sudo reboot
```

After reboot:

```bash
cat /etc/os-release
uname -a
```

## 2. Set hostname

```bash
sudo hostnamectl set-hostname app01
hostnamectl
```

Edit hosts file:

```bash
sudoedit /etc/hosts
```

Add both machines:

```text
192.168.0.107 app01
192.168.0.102 db01
```

Adjust IP addresses to match your actual network.

Test:

```bash
ping db01
ping app01
```

## 3. Give it a stable IP address

Best method: use the router’s DHCP reservation.

Example:

```text
app01 = 192.168.1.50
db01  = 192.168.1.51
```

Check current address:

```bash
ip addr
ip route
```

Do not continue seriously until both machines have predictable IP addresses.

## 4. Create proper users

Same rule as the Ubuntu server:

```text
admin account   = sudo work only
daily account   = normal SSH/login
service account = runs the app
```

Example:

```bash
sudo adduser isaac
sudo adduser takashi
sudo adduser elliot

sudo adduser takashi-admin
sudo usermod -aG wheel takashi-admin
```

On Alma/RHEL-family systems, the admin group is usually:

```text
wheel
```

Check:

```bash
getent group wheel
id takashi-admin
```

Create app service user later:

```bash
sudo useradd --system --create-home --home-dir /opt/clojure-crud --shell /sbin/nologin appuser
```

## 5. Set up SSH

Install and enable SSH if not already active:

```bash
sudo dnf install -y openssh-server
sudo systemctl enable --now sshd
sudo systemctl status sshd
```

From another machine:

```bash
ssh isaac@app01
```

Set up SSH keys:

```bash
ssh-copy-id isaac@app01
ssh-copy-id takashi@app01
ssh-copy-id takashi-admin@app01
```

Then harden SSH:

```bash
sudoedit /etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart:

```bash
sudo systemctl restart sshd
```

Keep one session open while testing a new one.

## 6. Learn `firewalld`

AlmaLinux uses `firewalld` by default.

Check:

```bash
sudo systemctl status firewalld
sudo firewall-cmd --state
sudo firewall-cmd --list-all
```

Allow SSH and HTTP:

```bash
sudo firewall-cmd --add-service=ssh --permanent
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

Do **not** open random ports.

## 7. Install basic tools

```bash
sudo dnf install -y git vim curl wget htop tree unzip tar net-tools bind-utils nmap-ncat tcpdump lsof
```

Useful commands he should practice:

```bash
ss -tulpen
ip addr
ip route
journalctl -xe
journalctl -f
top
htop
df -h
free -h
```

## 11. Install nginx

```bash
sudo dnf install -y nginx
sudo systemctl enable --now nginx
sudo systemctl status nginx
```

Allow HTTP if not already done:

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

From another computer, visit:

```text
http://app01
```

or:

```text
http://192.168.1.50
```

He should see the nginx test page.

## 12. Create app directory

```bash
sudo mkdir -p /opt/clojure-crud
sudo chown appuser:appuser /opt/clojure-crud
sudo chmod 750 /opt/clojure-crud
```

Put the built app JAR there later:

```text
/opt/clojure-crud/app.jar
```

## 13. Create environment file

```bash
sudoedit /etc/clojure-crud.env
```

Example:

```text
DB_HOST=db01
DB_PORT=3306
DB_NAME=crudapp
DB_USER=crud_user
DB_PASSWORD=real-password-here
APP_PORT=3000
```

Protect it:

```bash
sudo chown root:appuser /etc/clojure-crud.env
sudo chmod 640 /etc/clojure-crud.env
```

## 14. Create a systemd service

```bash
sudoedit /etc/systemd/system/clojure-crud.service
```

Example:

```ini
[Unit]
Description=Clojure CRUD App
After=network-online.target
Wants=network-online.target

[Service]
User=appuser
Group=appuser
WorkingDirectory=/opt/clojure-crud
EnvironmentFile=/etc/clojure-crud.env
ExecStart=/usr/bin/java -jar /opt/clojure-crud/app.jar
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Later, after the JAR exists:

```bash
sudo systemctl daemon-reload
sudo systemctl enable clojure-crud
sudo systemctl start clojure-crud
sudo systemctl status clojure-crud
journalctl -u clojure-crud -f
```

## 15. Configure nginx reverse proxy

Once the app runs on local port 3000:

```bash
sudoedit /etc/nginx/conf.d/clojure-crud.conf
```

Example:

```nginx
server {
    listen 80;
    server_name app01;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## 16. SELinux lesson

AlmaLinux uses SELinux. Do **not** disable it immediately. That is a bad habit.

Check:

```bash
getenforce
```

Expected:

```text
Enforcing
```

If nginx cannot proxy to the app, he may need:

```bash
sudo setsebool -P httpd_can_network_connect 1
```

That is a good learning moment. SELinux often blocks things that Ubuntu would allow. It is annoying, but it teaches real enterprise Linux behavior.

## 17. First app-server checklist

Before deploying the app, he should be able to prove:

```text
[ ] app01 has stable IP
[ ] app01 can ping db01
[ ] app01 can reach db01:3306
[ ] SSH key login works
[ ] password SSH is disabled
[ ] firewalld allows SSH and HTTP only
[ ] nginx test page loads
[ ] Java works
[ ] appuser exists and cannot login
[ ] SELinux is enforcing
```


--- STOP HERE

## 8. Install Java

Clojure apps usually run on the JVM.

```bash
sudo dnf install -y java-21-openjdk java-21-openjdk-devel
```

Check:

```bash
java -version
javac -version
```

If his app requires Java 17 instead:

```bash
sudo dnf install -y java-17-openjdk java-17-openjdk-devel
```

Do not randomly install many Java versions. Use the one the app expects.

## 9. Install Clojure tooling only if needed

For a production-style deployment, the app server should ideally run a built JAR:

```bash
java -jar app.jar
```

But for learning, he can install Clojure tools too.

Check if `clojure` is available:

```bash
clojure -Sdescribe
```

If not, install it using the official Linux install method later. But the better lesson is:

```text
Build on development machine → copy JAR to app01 → run with systemd
```

Do not turn the server into his development workstation.

Check
```text
[ ] Java works
```

## 10. Confirm the app server can reach the database server

From AlmaLinux app server:

```bash
nc -vz db01 3306
```

or:

```bash
nc -vz 192.168.1.51 3306
```

Expected:

```text
succeeded
```

If it fails, debug in this order:

```bash
ping db01
ip route
ss -tulpen
sudo firewall-cmd --list-all
```

On Ubuntu db01:

```bash
sudo ufw status verbose
ss -tulpen | grep 3306
sudo systemctl status mariadb
```

This is an important lesson: when two machines cannot talk, do not guess. Check name resolution, route, listening port, firewall, service.

