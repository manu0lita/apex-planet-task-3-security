# Cross-Site Request Forgery (CSRF)

## Objective

Demonstrate that a state-changing password request can be triggered without an anti-CSRF token at a vulnerable security level, then show token-based protection.

## Low-Level Demonstration

Set DVWA Security to **Low** and open:

```text
http://127.0.0.1:4280/vulnerabilities/csrf/
```

A controlled password-change request was demonstrated with:

```text
http://127.0.0.1:4280/vulnerabilities/csrf/?password_new=CSRFTest123&password_conf=CSRFTest123&Change=Change
```

The expected vulnerable behavior is that the request is accepted for the currently authenticated user.

### Source inspection

```bash
cd /root/DVWA
cat vulnerabilities/csrf/source/low.php
```

## Token-Based Protection

Set DVWA Security to **High**.

Inspect the password form with Firefox DevTools and look for a hidden field similar to:

```html
<input type="hidden" name="user_token" value="...">
```

Do not publish the actual token value.

Inspect the High implementation:

```bash
cat vulnerabilities/csrf/source/high.php
```

The protected flow checks the submitted token against the session token before processing the password change.

### Retest

Replay the earlier tokenless request at High. It should be rejected because the request does not contain the valid session-bound token.

## Mitigation Notes

- Prefer framework-provided CSRF protection where available.
- Require a CSRF token on state-changing requests.
- Validate the token on the server side against the expected session/user context.
- Consider secure cookie attributes such as SameSite as an additional layer.

## References

- OWASP CSRF Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
- DVWA CSRF source/help: https://github.com/digininja/DVWA/tree/master/vulnerabilities/csrf
