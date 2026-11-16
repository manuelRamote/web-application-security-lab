# Manual testing methodology

## 1. Baseline acquisition

Use Burp Proxy to capture normal UI actions against `127.0.0.1:3000`. Record the original request and response before making changes.

## 2. Controlled request modification

Send a relevant request to Repeater. Change **one variable at a time**, send the request, and compare the HTTP status, JSON structure and returned objects with the baseline. Preserve enough context to distinguish HTTP-layer results from application-level results.

## 3. Reproducibility

Repeat abnormal results and log whether they recur. The confirmed SQL injection in WEB-001 was retested with consistent outcomes. Do not extrapolate beyond the exact test conditions.

## 4. Authentication / authorization

Use distinct **lab-only** test accounts. Check endpoint behavior with and without authentication and, where a resource is owned by one account, compare the owner and other account. A `200 OK` alone is not a vulnerability, just as `400` alone does not prove correct authorization.

## 5. Evidence and reporting

For each investigation, record:

- HTTP method and path, plus parameter under test
- Baseline and modified inputs
- Status code and selected response fields
- Technical interpretation, uncertainty, and scope limits
- Suggested remediation for validated issues

Use the [evidence checklist](../evidence/README.md) before publishing screenshots.
