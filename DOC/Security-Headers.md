# HTTP Security Headers and Apache Hardening

## 1. External Header Analysis

`127.0.0.1` is local to the testing machine, so an external website such as SecurityHeaders.com cannot directly scan the local DVWA service.

For the header-analysis portion of the task, use a **public site that you own or are explicitly authorized to assess**.

Record:

- Security header grade/report.
- Present headers.
- Missing headers.
- Useful recommendations.

## 2. Local Apache Hardening

The local DVWA service can be hardened directly at the Apache layer.

Example response headers for the isolated lab:

```apache
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Permissions-Policy "camera=(), microphone=(), geolocation=()"
```

A CSP used earlier in the XSS exercise was:

```apache
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'; object-src 'none'; base-uri 'self'"
```

### Apache configuration workflow

```bash
docker exec -it dvwa-dvwa-1 bash
a2enmod headers
```

Create the configuration without relying on `nano`:

```bash
cat > /etc/apache2/conf-available/security-headers.conf <<'EOF'
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Permissions-Policy "camera=(), microphone=(), geolocation=()"
