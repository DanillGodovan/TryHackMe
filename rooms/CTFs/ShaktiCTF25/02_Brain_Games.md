# Room: Brain Games — ShaktiCTF 2025

## Goal

Bypass heavy input sanitization and achieve command execution (or information disclosure) in a restricted execution environment.

## Tools Used

- local experimentation
- Shell command variants and one-liners
- Alternative UNIX utilities to evade blacklisted commands

## Summary / Exploit

The challenge provided an extremely sanitized environment that runs user input via `bash -c`. Classic dangerous commands (e.g., `cat`, `ls`) were blocked/blacklisted. However, because `bash -c` was used, command chaining and shell metacharacters could still be effective when crafted to bypass simple filters.

We exploited command chaining using an allowed injection pattern like `;""` (or similar) to terminate sanitized input and append our commands. To avoid blacklisted binaries, we used allowed alternatives (for example `dir`, `cd`, `tac`) to read files or enumerate directories.

## Notes / Steps

1. Inspect how user input is passed to the shell — confirm `bash -c "<input>"` behavior.
2. Test simple injection attempts and observe filtering/sanitization behavior.
3. Use command separators (e.g., `;`) with a bypass `""` pattern to slip past naive filters: `userinput;"" <attacker_command>` (adjust depending on filter logic).
4. Because `cat`, `ls`, etc. were prohibited, use alternatives:
   - `dir` — list directory contents on some systems.
   - `tac` — can sometimes display file contents (reverse of `cat`).
   - `cd` combined with other allowed built-ins to reach files.
5. Chain commands to exfiltrate the flag to a reachable output (HTTP response, logs, or attacker-controlled listener).

## Useful commands / reminders

- Try many shell separators and wrappers (`;`, `&&`, `||`, `$(...)`, `` `...` ``) because filters often block specific tokens but not all meta-characters.
- Test alternative binaries: `dir`, `tac`, `nl`, `od`, `head`, `tail` (some may be allowed).
- If binary calls are blocked, try shell builtins (`echo`, `read`, `printf`, `exec`) to manipulate streams.

## Lessons Learned

- Naive blacklists of command names are brittle — attackers can often use synonyms, builtins, or chaining to bypass them.
- When `bash -c` is used, the shell's parsing gives many opportunities to escape sanitizers.
- Defenses should use whitelists, proper escaping, or avoid shell execution entirely.
