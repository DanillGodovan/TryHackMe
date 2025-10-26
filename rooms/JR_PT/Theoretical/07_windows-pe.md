# Room 07 – Windows Privilege Escalation

## Goal

Explore common privilege escalation paths on Windows machines.

## Methods

- Token impersonation
- `whoami /priv`
- Exploiting weak service permissions

## Tools Used

- `winPEAS.exe`
- `accesschk.exe`

## Lessons Learned

- Always check for unquoted service paths
- Privileges often hidden in overlooked tokens
