# WEB-001 — SQL Injection in product search

**Status:** Confirmed and reproduced in the local training environment  
**Category:** Injection  
**CWE:** CWE-89 — Improper Neutralization of Special Elements used in an SQL Command  
**Affected endpoint:** `GET /rest/products/search`  
**Parameter:** `q`  
**Tool:** Burp Suite Community / Repeater  
**Severity:** Not formally scored (impact not fully investigated)

## Summary

The product-search parameter can influence the SQL expression evaluated by the application. A malformed input caused the service to disclose an SQLite error and part of its SQL query; controlled true/false conditions produced different result sets and were successfully repeated.

## Reproduction evidence

| Test | Query parameter | Observed result |
| --- | --- | --- |
| Normal search | `q=apple` | `200 OK`; matching product records returned |
| Isolated quote | `q=%27` | `200 OK`; `data: []` |
| Malformed input | `q=%27%29%29` | `500 Internal Server Error`; `SQLITE_ERROR`, syntax error and SQL text in JSON |
| Boolean condition true | `q=%27%29%29%20AND%201%3D1--%20` | `200 OK`; products returned |
| Boolean condition false | `q=%27%29%29%20AND%201%3D2--%20` | `200 OK`; `data: []` |

The true/false behavior was **confirmed again in a second run**. These results demonstrate that supplied input can change the query's logic rather than merely selecting a normal search term.

## Supported impact

- **Confirmed:** SQL query manipulation in this deliberately vulnerable local application.
- **Confirmed:** database error details and query text leaked in the malformed-input response.
- **Not demonstrated:** database enumeration, extraction of confidential information, authentication bypass, or escalation of privileges.

No exploitation against a live service was performed.

## Recommended remediation

1. Use parameterized SQL queries / bound parameters rather than concatenating user-supplied input into SQL statements.
2. Restrict and validate user input where appropriate; do not treat validation alone as a substitute for parameterization.
3. Use standardized error handling; return a generic client error and log detailed database failures only in protected server-side logs.
4. Add regression tests for normal input, malformed syntax and SQL boolean-expression attempts.

## Evidence status

Five screenshots from the 16 November 2026 Burp session have been cropped, inspected and prepared locally: `01-http-history-baseline.png`, `02-repeater-search-normal.png`, `03-sqli-sqlite-error.png`, `04-sqli-true-condition.png` and `05-sqli-false-condition.png`. **These are not yet uploaded to GitHub**; they will be linked here after upload and verification. The images show request and response details without session tokens or cookies.
