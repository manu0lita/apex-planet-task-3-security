# Burp Suite: Proxy and Intruder

## 1. Proxy — Intercept and Modify Login Request

### Objective

Demonstrate that an HTTP request can be intercepted and modified before it reaches the application.

### Procedure

1. Open Burp Suite.
2. Go to **Proxy → Intercept**.
3. Enable interception.
4. Open the local DVWA login page.
5. Submit a normal login request.
6. Inspect the intercepted request.
7. Modify the password value to a different test value.
8. Forward the modified request.

A typical request contains fields similar to:

```http
POST /login.php HTTP/1.1
Host: 127.0.0.1:4280

username=admin&password=password&Login=Login
```

The exact request may contain additional fields depending on the DVWA version/configuration.

### Security Lesson

HTTP requests should never be assumed to be trustworthy simply because they originated from the application's own browser interface. The server must enforce authentication, authorization, validation, and other controls independently of client-side UI behavior.

---

## 2. Intruder — Controlled Fuzzing

### Objective

Use Burp Intruder to automatically send multiple versions of a request while varying one input parameter.

### Payload Position

The username parameter was selected as the payload position:

```http
username=§admin§&password=password&Login=Login
```

`§` marks the location where Burp inserts each payload.

### Attack Type

**Sniper** was used because only one payload position was tested.

### Payload Type

**Simple list**.

Example controlled payloads:

```text
admin
gordonb
pablo
smithy
1337
```

### What Intruder Does

Burp generates one request per payload:

```text
username=admin
username=gordonb
username=pablo
username=smithy
username=1337
```

The resulting table contains response information such as status, response length, and timing. These values are used to spot unusual responses for manual investigation.

### Security Lesson

Fuzzing is an input-testing technique. A difference in response size or status is an indicator for further analysis; it is not, by itself, proof of a vulnerability.

## References

- PortSwigger Intruder positions: https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/positions
- PortSwigger Intruder fuzzing: https://portswigger.net/burp/documentation/desktop/tools/intruder/uses/fuzzing
- PortSwigger attack types: https://portswigger.net/burp/documentation/desktop/tools/intruder/configure-attack/attack-types
