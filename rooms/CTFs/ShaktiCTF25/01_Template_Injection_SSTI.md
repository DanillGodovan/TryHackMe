# Room: Template Injection (SSTI) — ShaktiCTF 2025

## Goal

Exploit server-side template injection in a DoT (DoT.js) application to read environment variables and obtain the FLAG.

## Tools Used

- Source code review (vulnerable DoT.js app)
- Local test deployments / template payloads
- Reverse shell / web-shell techniques (if RCE achieved)

## Summary / Exploit

The app used DoT (DoT.js) templates and insecurely injected environment variables into templates. The template allowed direct access to Node process environment via an expression like:

```text
{{=global.process.env.FLAG}}
```

This revealed the flag when rendered by the vulnerable template engine.

## Notes / Steps

- Obtain the vulnerable source (provided). Inspect templates and rendering flow.
- Find that templates accept untrusted input and that DoT rendering exposes global/process.
- Test payloads by injecting expressions into template placeholders, e.g. {{=global.process.env.FLAG}}.
- If direct rendering of process.env.FLAG is blocked, attempt other DoT expression variants or nested lookups.
- Retrieve FLAG from rendered output.

## Useful commands / reminders

- When testing locally, run the app and inject template payloads via the web form or API that feeds the template.
- Search the code for doT.template, doT.compile, or any .render() calls that concatenate user input into templates.

## Lessons Learned

- Template engines that expose global context (or allow arbitrary expression evaluation) are extremely dangerous.
- Even seemingly safe template placeholders can leak secrets if the template environment includes global/process.
- Always sanitize template inputs and use a safe rendering context (avoid exposing Node globals to template scope).
