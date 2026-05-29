# SysAdmin-LAB

## To read this lab on your Obsidian
1. Open your Obisidian Vault (~/Obsidian)
2. `git clone https://github.com/lambdalab21/SysAdmin-LAB.git`
3. Click on the Vault > Manage vaults.. under the left tree
4. click on "Open" button.  Navigate to ~/Obsidian/SysAdmin-Lab
   
## Setup
### Physical Setup 
app01: 
  almalinux
  nginx
  
db01: 
  ubuntu
  PostgreSQL/MariaDB

### Virtual Setup
app01-alma:
  AlmaLinux 10
  main Clojure REST API server

app02-rocky:
  Rocky Linux 10
  comparison server

db01:
  Debian or Ubuntu Server
  PostgreSQL/MariaDB

web01:
  AlmaLinux or Rocky
  nginx reverse proxy

mon01:
  Ubuntu/Debian
  monitoring/logging
