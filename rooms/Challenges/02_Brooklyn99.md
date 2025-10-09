# Brooklyn99

## Goal

Gain user access, then escalate to root.

## Tools Used

- Hydra (password bruteforce)
- Local utilities on target (less, sudo)
- Standard shell tools

## Summary / Exploit

Password brute-force discovered a weak password (`987654321`) for user `jake`. `jake` is in sudoers and can run `less`, which allows spawning a shell (`!sh`) from within `less` → root shell.

## Notes / Steps

1. Perform password guessing (Hydra) against the target service → discovered password `987654321`.
2. Login as `jake` (SSH or service).
3. Check `sudo -l` — `jake` can run `less` with elevated privileges.
4. Run `sudo less <file>` and execute `!sh` inside `less` to spawn a shell with root privileges.
5. Use `su` or directly read root flag.

## Useful commands / reminders

- Hydra example: `hydra -l jake -P /path/to/wordlist.txt ssh://<IP>`
- In `less`, type `!sh` (or `!bash`) to drop into a shell.
- Always run `sudo -l` early to enumerate permitted commands.

## Lessons Learned

- Weak passwords remain a frequent initial access vector.
- Sudo permissions to otherwise harmless binaries (less, vi, more, man) commonly lead to trivial privilege escalation.
- Enumerate sudo rights before attempting complex escalation paths.
