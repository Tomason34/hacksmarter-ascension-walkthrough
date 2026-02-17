# Ascension – HackSmarter Walkthrough

## Overview
Target: Ascension  
Techniques Used:
- NFS Misconfiguration
- SSH Key Cracking
- Cron Job Abuse
- WordPress Database Enumeration
- Linux Capabilities Privilege Escalation

---

## 1. Initial Enumeration

nmap -sC -sV -p- 10.1.88.237

Discovered:
- FTP (21)
- SSH (22)
- HTTP (80)
- NFS (2049)
- MySQL (3306)

---

## 2. NFS Exploitation

showmount -e 10.1.88.237

Found open share:
/srv/nfs/user1 *

Mounted and found SSH private key.

Cracked with rockyou:

ssh2john id_rsa > ssh.hash  
john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash

Gained SSH access as user1.

---

## FLAG 1
RkxBRzF7aGpzeXU4OTIzMzRoam9obnNkOHkyOTNoNH0=

---

## 3. Cron Abuse → user2

Discovered /tmp/backup.sh executed by user2.

Injected malicious script to add SSH key.

Logged in as user2.

---

## FLAG 2
RkxBRzJ7c2RoaDk4MjM0bmpvaG4za2pkajIzM2ZkfQ==

---

## 4. WordPress Database Exploitation

Found DB credentials in:
/var/www/html/wp-config.php

mysql -u wpuser -p

Extracted user3 credentials.

---

## FLAG 4
RkxBRzR7d2ViamhuYXNkMzg5MjM0a25kam9pM2R9

---

## 5. Lateral Movement → user3

su user3

---

## FLAG 5
RkxBRzV7am9obmFiY2RzamhmczgyMzRram5ib3p9

---

## 6. Privilege Escalation (Linux Capabilities Abuse)

Found suspicious binary:
~/python3

getcap ~/python3

Found:
cap_setuid=ep

Abused capability:

~/python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

Root obtained.

---

## FLAG 6
RkxBRzZ7c2RmanVkaGZzODIzNGtqbmJvaG5kamZ9

---

## FLAG 3
RkxBRzN7Z2pkb2huYXNkOTgyMzRram5iY2tkZn0=

---

## Final Summary

Attack Chain:
1. NFS exposure → SSH key theft
2. Key cracked via rockyou
3. Cron abuse → user2
4. WordPress DB creds → user3
5. Capability abuse → root

All flags captured successfully.
