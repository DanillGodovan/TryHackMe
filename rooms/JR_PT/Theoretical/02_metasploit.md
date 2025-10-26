# Room 02 – Introduction to Metasploit

## Goal

Gain familiarity with the Metasploit Framework by exploiting a known vulnerable service.

## Tools Used

- `msfconsole`
- `searchsploit` to find matching CVEs

## Exploit Used

exploit/unix/ftp/vsftpd_234_backdoor
Gained shell and pivoted into user flag.

## Notes

- `set RHOST` and `set LHOST` properly
- Use `sessions` to manage multiple shells

## Lessons Learned

- Efficient workflow with Metasploit modules
- Shell management and session tracking
