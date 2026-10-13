# Web Security Lab Write-ups

Hands-on write-ups of web application vulnerabilities I've practised on the
[PortSwigger Web Security Academy](https://portswigger.net/web-security).
Each covers what the vulnerability is, how I exploited it, why the payload works,
and how it should be fixed.

> All testing was done in PortSwigger's deliberately vulnerable training labs.
> These are learning exercises, not findings against any real-world system.

## Tools

- **Burp Suite Community Edition** — Proxy & Repeater for intercepting and modifying requests
- PortSwigger Web Security Academy labs

## Write-ups

| # | Vulnerability | Category | Level |
|---|---------------|----------|-------|
| 01 | [Retrieving hidden data](01-sqli-retrieving-hidden-data.md) | SQL Injection | Apprentice |
| 02 | [Login bypass](02-sqli-login-bypass.md) | SQL Injection | Apprentice |

## Key takeaway

Most injection vulnerabilities come down to one thing: **user input crossing the
boundary from data into code.** When input is concatenated straight into a query
or command, an attacker can change what the application actually executes. The fix
is almost always to keep that boundary intact — e.g. parameterised queries for SQL.

## About me

Software Engineering & AI graduate (University of Leicester), CompTIA Security+ certified,
moving into cybersecurity / AppSec.

- GitHub: [github.com/d-adz](https://github.com/d-adz)
- LinkedIn: [linkedin.com/in/demiadewole](https://linkedin.com/in/demiadewole)
