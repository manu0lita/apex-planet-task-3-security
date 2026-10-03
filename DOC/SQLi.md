# SQL Injection (SQLi)

## Objective

Determine whether the DVWA SQL Injection functionality allows user-controlled input to alter the intended SQL query and whether database records can be exposed.

## Vulnerable Scenario

At DVWA Low security, the application builds the database query using user-controlled input. This allows SQL syntax to change the meaning of the query.

## Test Inputs Used

These values were entered in the DVWA SQL Injection form:

```text
1
1'
1' ORDER BY 1 #
1' ORDER BY 2 #
1' ORDER BY 3 #
1' OR 1=1 ORDER BY 2 #
1' UNION SELECT user,password FROM users #
```

### What the tests demonstrated

- `1` established baseline behavior.
- `1'` produced a SQL syntax error, indicating that the input reached the SQL parser.
- `ORDER BY 1/2/3` was used to reason about the number of output columns; `ORDER BY 3` produced an unknown-column error because the query exposed two output columns.
- `OR 1=1` caused the predicate to become true for multiple rows.
- `UNION SELECT user,password FROM users` demonstrated extraction of the `user` and `password` fields from the local DVWA database.

> Treat the `password` values as credential material even when they are stored as hashes. Do not publish unnecessary credential-bearing screenshots.

## Vulnerable Code Inspection

```bash
cd /root/DVWA
cat vulnerabilities/sqli/source/low.php
```

## Mitigation

The recommended primary defense is a **prepared/parameterized SQL statement**, optionally combined with strict input validation for values with a constrained type such as an integer.

The DVWA secure implementation was inspected with:

```bash
cat vulnerabilities/sqli/source/impossible.php
```

The secure path uses numeric validation and a PDO prepared statement with a bound parameter before execution.

## Why Prepared Statements Work

The SQL structure is defined separately from the user's value. The database therefore treats the supplied value as data rather than allowing it to become SQL syntax.

## Retest

After switching DVWA to the secure implementation, repeat a malicious SQLi input. The payload should no longer change the intended query semantics.

## References

- OWASP SQL Injection Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- DVWA SQL Injection source: https://github.com/digininja/DVWA/tree/master/vulnerabilities/sqli
