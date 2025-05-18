# Room 04 – SQL Injection

## Goal

Practice SQL injection via login forms and URL parameters.

## Process

- Used `' OR 1=1--` to bypass login
- Extracted database via `UNION SELECT`
- Found admin credentials and reused them in other areas

## Lessons Learned

- Differences between error-based and blind SQLi
- Using `sqlmap` as a last resort
