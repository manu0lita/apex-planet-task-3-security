# DVWA Web Application Security Assessment

A controlled web application security assessment performed against **Damn Vulnerable Web Application (DVWA)** in an isolated Kali Linux + Docker lab for the ApexPlanet Cybersecurity & Ethical Hacking Internship – Task 3.

## Objectives

- Demonstrate SQL Injection and document prepared-statement mitigation.
- Demonstrate Stored and Reflected Cross-Site Scripting (XSS) and safe output handling.
- Demonstrate Cross-Site Request Forgery (CSRF) and token-based protection.
- Demonstrate Local File Inclusion (LFI) and Remote File Inclusion (RFI) in a controlled lab.
- Use Burp Suite Proxy to intercept/modify HTTP requests.
- Use Burp Suite Intruder for controlled input fuzzing.
- Analyze HTTP security headers and harden Apache response headers.

## Lab Environment

- Kali Linux virtual machine
- DVWA running in Docker
- DVWA URL: `http://127.0.0.1:4280`
- Security level changed between **Low** (vulnerable demonstration) and **High/Impossible** (mitigation comparison)
- Testing was restricted to the local DVWA lab.

## Methodology

For each issue, the assessment followed:

1. Establish normal application behavior.
2. Test the deliberately vulnerable implementation.
3. Capture evidence.
4. Inspect the relevant source code.
5. Apply or review the mitigation.
6. Retest and record the result.

## Repository Structure

```text
.
├── README.md
├── docs/
│   ├── SQLi.md
│   ├── XSS.md
│   ├── CSRF.md
│   ├── File-Inclusion.md
│   ├── Burp-Suite.md
│   ├── Security-Headers.md
│   └── Commands-and-Evidence.md
└── evidence/
    └── .gitkeep
```

## Safety Note

All exploitation described here was performed against the user's own local DVWA instance for educational and authorized testing. The RFI demonstration used a harmless PHP file that only returned a test string.

## Key References

- OWASP SQL Injection Prevention: https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- OWASP XSS Prevention: https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- OWASP CSRF Prevention: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
- OWASP HTTP Security Response Headers: https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html
- PortSwigger Burp Intruder fuzzing: https://portswigger.net/burp/documentation/desktop/tools/intruder/uses/fuzzing
- DVWA: https://github.com/digininja/DVWA

## Evidence

Place screenshots in `evidence/`. Recommended names are listed in `docs/Commands-and-Evidence.md`.
