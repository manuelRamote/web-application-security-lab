# Evidence — Upload and redaction checklist

**Current status:** All five WEB-001 screenshots from **16 November 2026** have been **uploaded and linked** in this private repository. The WEB-002 and WEB-003 screenshots remain pending separate anonymization.

## Evidence inventory and upload status

| File | Evidence | Status |
| --- | --- | --- |
| [`01-http-history-baseline.png`](01-http-history-baseline.png) | Juice Shop request/response in Burp Proxy | Uploaded and linked |
| [`02-repeater-search-normal.png`](02-repeater-search-normal.png) | Normal `q=apple` search | Uploaded and linked |
| [`03-sqli-sqlite-error.png`](03-sqli-sqlite-error.png) | Malformed input and SQLite error | Uploaded and linked |
| [`04-sqli-true-condition.png`](04-sqli-true-condition.png) | True-condition SQL injection test | Uploaded and linked |
| [`05-sqli-false-condition.png`](05-sqli-false-condition.png) | False-condition SQL injection test | Uploaded and linked |
| `06-authenticated-user-response.png` | Authenticated user property names, secrets redacted | Not prepared; credential redaction pending |
| `07-unauthenticated-401.png` | `/rest/saveLoginIp` rejected without authentication | Not prepared |
| `08-cross-user-address-rejected.png` | User A blocked from B's address | Not prepared; credential redaction pending |
| `09-owner-address-allowed.png` | User B successfully accesses own address | Not prepared; credential and contact redaction pending |

The five WEB-001 PNGs show the local unauthenticated tests and were inspected for token or cookie exposure during cropping. Perform a final release review of every image before changing repository visibility.

## Privacy checklist

- [ ] Obscure **all** JWTs, Authorization headers, `token` cookies, session identifiers and any secret-bearing headers. Do not use reversible pixelation for actual credentials.
- [ ] Obscure emails, phone numbers, physical address details and passwords/password hashes, even when fictional.
- [ ] Ensure browser tabs, URL parameters, desktop paths and terminal history contain no unrelated private information.
- [ ] Include only the necessary response fields and HTTP status code for technical explanation.
- [ ] Verify each redacted output as saved, not merely the original editor preview.
- [ ] Inspect file metadata and exclude raw Burp project files and unredacted exports.
- [ ] Only publish evidence once the review is complete.

Never upload live credentials or copy authentic tokens into text documentation. Prefer new short-lived lab sessions when creating publication-ready screenshots.
