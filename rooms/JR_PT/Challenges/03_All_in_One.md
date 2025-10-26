# All in One

## Goal

Enumerate web services, exploit WordPress (plugins / LFI / upload), obtain web shell, capture user and root flags.

## Tools Used

- `nmap`
- `gobuster` (directory bruteforce)
- `wpscan`
- Manual source inspection
- Reverse shell (PHP)
- Standard Linux utilities on target (`find`, `ssh`, `socat`, etc.)

## Summary / Exploit

Nmap revealed ports 21, 22, 80. Web enumeration found `/hackathons` and `/wordpress`. `/hackathons` contained a hint referencing Vigenère cipher (hint spelled as “vinegar”). Decoding produced a password or key. WPScan identified vulnerable plugins: Mail Masta (LFI) and Reflex Gallery (image upload / arbitrary file upload). Used LFI and template/404 upload to place a PHP revshell → gained shell. From the shell, found `user.txt` and `hint.txt` in `/home/elyana`. Root escalation vectors included `socat` in sudo, SUID binaries, and an editable cron-run `script.sh`.

## Notes / Steps

1. **Port scan**: `nmap` → 21 (ftp, empty), 22 (ssh), 80 (http).
2. **Web enumeration**: `gobuster` discovered `/hackathons` and `/wordpress`.
3. **/hackathons**: found hint referencing “vinegar” → interpret as Vigenère cipher → decode with key from page/source → obtain password/key.
4. **WordPress enumeration**: `wpscan` shows:
   - Mail Masta — LFI vulnerability.
   - Reflex Gallery — allows file uploads (images), possibly enabling PHP upload.
5. **Exploit flow**:
   - Use LFI to read sensitive files (`/etc/passwd`, `wp-config.php`) or find credentials.
   - Upload malicious PHP via vulnerable plugin/template/404 handling to achieve RCE.
   - Deploy a PHP revshell and connect back.
6. **Stabilize shell**:
   - Upgrade to PTY: `python -c "import pty; pty.spawn('/bin/bash')"`
   - Navigate to `/home/elyana` → read `user.txt` and `hint.txt`.
7. **Find hidden password**:
   - Example find command: `find / -user elyana -type f 2>&1 | grep -v "Permission"`
   - Use discovered file contents to SSH/login as `elyana` if applicable.
8. **Privilege escalation to root**:
   - Check `sudo -l` to see allowed commands (`socat` listed).
   - Look for SUID binaries or an editable cron job that executes `script.sh`.
   - Possible vectors: use `socat` to spawn a root shell, exploit writable cron script to inject commands, or abuse SUID binary.

## Useful commands / reminders

- WordPress enumeration:  
  `wpscan --url http://<IP>/wordpress --enumerate u,vp,ap,tt`
- PTY spawn: `python -c "import pty; pty.spawn('/bin/bash')"`
- Find files owned by a user: `find / -user elyana -type f 2>&1 | grep -v "Permission"`
- When uploading webshells, ensure correct file extension and that upload is reachable/executable by the webserver.

## Lessons Learned

- LFI combined with arbitrary upload or vulnerable plugins is a common path to RCE on WordPress.
- Hints on the website often point directly to cipher types or keys (e.g., Vigenère).
- After obtaining a user shell, prioritize checking cron jobs, sudoers, and SUID binaries to plan privilege escalation.
