# Cross-Site Scripting (XSS)

## 1. Stored XSS

### Objective

Test whether content submitted to the DVWA guestbook is stored and later rendered as executable browser content.

### Test Input

```html
<script>alert('Stored XSS')</script>
```

### Procedure

1. Set DVWA Security to **Low**.
2. Open `/vulnerabilities/xss_s/`.
3. Submit the payload in the guestbook message field.
4. Observe the browser alert when the stored entry is rendered.
5. Inspect the source code.

### Source inspection

```bash
cd /root/DVWA
cat vulnerabilities/xss_s/source/low.php
cat vulnerabilities/xss_s/source/impossible.php
```

### Root Cause

Escaping for the database/SQL layer is not the same as safely encoding content for the HTML/browser context. The security-sensitive boundary is the point where untrusted stored data is rendered into HTML.

### Mitigation

For HTML text output, use context-appropriate output encoding. DVWA's secure implementation uses `htmlspecialchars()` before rendering stored values.

Example concept:

```php
$name = htmlspecialchars($name, ENT_QUOTES, 'UTF-8');
```

CSP can be added as an additional defense-in-depth control, but it should not be treated as the primary replacement for correct output encoding.

---

## 2. Reflected XSS

### Objective

Determine whether a query parameter is reflected directly into the HTTP response without safe output encoding.

### Normal Input

```text
Manu
```

### Test Input

```html
<script>alert('Reflected XSS')</script>
```

### Source inspection

```bash
cd /root/DVWA
cat vulnerabilities/xss_r/source/low.php
cat vulnerabilities/xss_r/source/impossible.php
```

The vulnerable pattern is direct concatenation of the request parameter into HTML, for example:

```php
$html .= '<pre>Hello ' . $_GET['name'] . '</pre>';
```

### Mitigation

Encode the parameter before placing it into HTML:

```php
$name = htmlspecialchars($_GET['name'], ENT_QUOTES, 'UTF-8');
$html .= '<pre>Hello ' . $name . '</pre>';
```

### CSP

A lab CSP used during testing was:

```apache
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'; object-src 'none'; base-uri 'self'"
```

It was verified through HTTP response headers and later rolled back after the XSS demonstration so the vulnerable Low-level behavior could be shown independently.

## References

- OWASP XSS Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- OWASP CSP Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html
- DVWA Stored XSS: https://github.com/digininja/DVWA/tree/master/vulnerabilities/xss_s
- DVWA Reflected XSS: https://github.com/digininja/DVWA/tree/master/vulnerabilities/xss_r
