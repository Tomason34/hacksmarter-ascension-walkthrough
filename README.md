⚠️ Disclosure: This write‑up was created with AI assistance, but I did all the actual lab work.
# HackSmarter: Ascension — Walkthrough

**Attack chain:** NFS → SSH key crack → cron abuse → MySQL → capability privesc → root  
**Environment:** Controlled lab / CTF

---

## Summary

This machine was compromised through a series of misconfigurations:

1. Public NFS export exposed an SSH private key for `user1`
2. Weak key passphrase was cracked with a wordlist
3. A scheduled job executed `/tmp/backup.sh` as `user2` (world-writable path)
4. WordPress DB credentials were found in `wp-config.php`, revealing `user3` creds + flags
5. A `python3` binary with `cap_setuid=ep` enabled root escalation

---

## Key Steps

### 1) Enumeration
- Found FTP/SSH/HTTP/NFS and later MySQL.

### 2) NFS → SSH key access (user1)
- Mounted export and extracted `id_rsa`
- Cracked passphrase and SSH’d into `user1`

**Flag 1**
RkxBRzF7aGpzeXU4OTIzMzRoam9obnNkOHkyOTNoNH0=



---

### 3) Cron abuse → user2
- `/tmp/backup.sh` executed by `user2`
- Used it to add my public key to `user2` authorized_keys
- SSH’d into `user2`

**Flag 2**
RkxBRzJ7c2RoaDk4MjM0bmpvaG4za2pkajIzM2ZkfQ==



---

### 4) WordPress DB → user3
- Read `/var/www/html/wp-config.php`
- Connected to MySQL as `wpuser`
- Pulled Flag 4 + `user3` credentials, then `su user3`

**Flag 4**
RkxBRzR7d2ViamhuYXNkMzg5MjM0a25kam9pM2R9


**Flag 5**
RkxBRzV7am9obmFiY2RzamhmczgyMzRram5ib3p9



---

### 5) Capability privesc → root
- Found `~/python3` for user3
- `getcap` showed `cap_setuid=ep`
- Used it to spawn a root shell

**Flag 6**
RkxBRzZ7c2RmanVkaGZzODIzNGtqbmJvaG5kamZ9


**Flag 3**
RkxBRzN7Z2pkb2huYXNkOTgyMzRram5iY2tkZn0=


---

## Defensive Notes

- Restrict NFS exports (no `*`), use proper permissions and root-squash where appropriate
- Never store private keys on world-accessible shares
- Cron jobs must not execute scripts from world-writable paths like `/tmp`
- Do not store DB creds in world-readable locations; use least privilege DB users
- Audit Linux capabilities (`getcap -r / 2>/dev/null`) — treat them like SUID


