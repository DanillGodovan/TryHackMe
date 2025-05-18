# Room 01 – Simple CTF & Enumeration

## Objective

Learn basic enumeration techniques through an accessible CTF environment. Focused on:

- Service discovery (Nmap)
- Directory brute-forcing
- Web service analysis

## Tools Used

- `nmap` for port scanning
- `gobuster` for directory fuzzing
- `whois`, `nslookup`, `dig` for recon
- Firefox + Burp Suite for manual analysis

## Key Findings

PORT STATE SERVICE
22/tcp open ssh
80/tcp open http

Found a `/backup.zip` file that contained credentials. Used to log into an SSH session.

## Lessons Learned

- Importance of checking all accessible paths
- Understand file permissions and ZIP file analysis
