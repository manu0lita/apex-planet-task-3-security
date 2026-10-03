# Attack Scenarios + Mitigation Notes

This page is a quick-reference summary for the Task 3 repository.

| Area | Attack scenario | Root cause | Mitigation demonstrated / recommended |
|---|---|---|---|
| SQL Injection | Crafted `id` input changed SQL behavior; UNION extraction exposed local `user,password` fields | User input is incorporated into dynamic SQL | Prepared/parameterized queries; allow-list validation where applicable |
| Stored XSS | Guestbook payload was stored and executed when rendered | Untrusted stored data was rendered into HTML without context-appropriate output encoding | HTML output encoding such as `htmlspecialchars()` for the HTML context; CSP as defense-in-depth |
| Reflected XSS | Query parameter was reflected directly into the response and executed in the vulnerable level | Request data was concatenated into HTML without safe output encoding | Context-appropriate output encoding; CSP as defense-in-depth |
| CSRF | State-changing password request could be sent without a valid anti-CSRF token at Low | State-changing request lacked server-side request-origin authenticity protection | Framework CSRF protection or session-bound CSRF tokens validated server-side |
| LFI | `page=/etc/passwd` caused disclosure of a local file | User-controlled input influenced the file included by the application | Allowlist permitted files/pages; avoid direct user-controlled include paths |
| RFI | Remote harmless PHP test file was included/executed after URL inclusion was temporarily enabled | User-controlled include path plus URL-based inclusion capability | Keep URL inclusion disabled unless explicitly required; strict allowlisting |
| Burp Proxy | Login request was intercepted and a parameter was modified before forwarding | HTTP requests are client-controlled and can be altered in transit | Enforce server-side validation, authentication, authorization, and anti-CSRF protections |
| Burp Intruder | Multiple controlled username values were automatically inserted into one request parameter | Manual input testing does not scale | Automated fuzzing followed by response comparison and manual investigation |
| HTTP Headers | Security response headers were reviewed and Apache headers were added locally | Missing or weak browser security policies can increase attack surface | Appropriate response headers such as `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy`, `Permissions-Policy`, and carefully designed CSP |

## Severity Note

Severity should be assigned according to the actual impact, affected trust boundary, exploitability, and application context. A proof-of-concept alone does not determine severity.

## Verification Principle

A mitigation is not considered complete merely because configuration or code was changed. Retest the original behavior and record whether the vulnerable behavior is prevented.
