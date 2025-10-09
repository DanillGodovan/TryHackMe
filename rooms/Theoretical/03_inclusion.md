# Room 03 – Local File Inclusion (LFI)

## Goal

Understand and exploit LFI to read sensitive files and gain access.

## Process

- Identified LFI via `page=../../../../etc/passwd`
- Used PHP log poisoning for RCE
- Gained reverse shell using `nc`

## Lessons Learned

- Relative path traversal logic
- Practical RCE via logs and wrappers
