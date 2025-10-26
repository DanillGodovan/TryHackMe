# JPGChat

## Goal

OSINT + minimal privilege escalation. Find leaks in source code, achieve RCE and capture user/root flags.

## Tools Used

- Web / OSINT (GitHub search based on service hint)
- Reverse shells (PHP / Python / netcat)
- Basic utilities in the gained shell session

## Summary / Exploit

Located repository/code referenced by the service (port → GitHub). Source contained a command-injection vector — used to deploy a reverse shell (revshell) and obtain the user flag. Root escalation is possible by hijacking/replacing the `compare` module in a script that runs with elevated privileges.

## Notes / Steps

1. From the service hint (port) locate the related GitHub repository and review source code.
2. Find command injection or unsafe execution (`exec`, `system`, `shell_exec`, `popen`, etc.).
3. Craft payload to execute a reverse shell and deploy it.
4. Obtain user shell → read `user.txt`.
5. For root: analyze local scripts/modules and identify `compare` module hijack opportunity → replace/modify to achieve privileged execution → obtain root flag.

## Useful commands / reminders

- Search the repo for suspicious functions: `grep -R "exec\|system\|shell_exec\|popen" .`
- When you get a simple shell, upgrade to a PTY:  
  `python -c "import pty; pty.spawn('/bin/bash')"`
- Common reverse shell one-liners: PHP, Python, nc depending on target.

## Lessons Learned

- Public repositories and web hints often reveal critical vulnerabilities.
- OSINT combined with simple RCE can quickly yield user access.
- Privilege escalation often involves replacing or abusing locally executed modules/scripts running as a higher-privileged user.
