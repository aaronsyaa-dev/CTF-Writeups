# Pickle Rick - TryHackMe

**Difficulty:** Easy
**Category:** Web exploitation, Linux basics

## Recon

- Found username in HTML source code comment: `R1ckRul3s`
- Found password in `/robots.txt`: `Wubbalubbadubdub`

## Access

- Logged in via `/login.php`
- Access to a Command Panel allowing Linux command execution

## Ingredients

- Ingredient 1: read `Sup3rS3cretPickl3Ingred.txt` via `less`
- Ingredient 2: found in `/home/rick/second ingredients`
- Ingredient 3: found in `/root/3rd.txt` using `sudo`

## What I learned

- Always check page source and exposed files
- Basic Linux navigation
- Using sudo for privilege escalation
