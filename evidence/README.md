# Evidence — Upload and redaction checklist

**Current status:** Five WEB-001 screenshots (baseline, normal query, SQLite error, true and false SQL conditions) have been **cropped and prepared locally** from the captured Burp session on 16 November 2026. **They are not yet committed to GitHub.** The remaining WEB-002/WEB-003 screenshots are pending separate anonymization.

## Evidence inventory and upload status

| File | Evidence | Status |
| --- | --- | --- |
| `01-http-history-baseline.png` | Juice Shop request/response in Burp Proxy | Locally prepared · upload pending |
| `02-repeater-search-normal.png` | Normal `q=apple` search | Locally prepared · upload pending |
| `03-sqli-sqlite-error.png` | Malformed input and SQLite error | Locally prepared · upload pending |
| `04-sqli-true-condition.png` | True-condition SQL injection test | Locally prepared · upload pending |
| `05-sqli-false-condition.png` | False-condition SQL injection test | Locally prepared · upload pending |
| `06-authenticated-user-response.png` | Authenticated user property names, secrets redacted | Not prepared; credential redaction pending |
| `07-unauthenticated-401.png` | `/rest/saveLoginIp` rejected without authentication | Not prepared |
| `08-cross-user-address-rejected.png` | User A blocked from B's address | Not prepared; credential redaction pending |
| `09-owner-address-allowed.png` | User B successfully accesses own address | Not prepared; credential and contact redaction pending |

The first five prepared PNGs contain only the visible unauthenticated local traffic and have been inspected for token and cookie exposure. Verify each PNG again before the repository is made public.

## Privacy checklist

- [ ] Obscure **all** JWTs, Authorization headers, `token` cookies, session identifiers and any secret-bearing headers. Do not use reversible pixelation for actual credentials.
- [ ] Obscure emails, phone numbers, physical address details and passwords/password hashes, even when fictional.
- [ ] Ensure browser tabs, URL parameters, desktop paths and terminal history contain no unrelated private information.
- [ ] Include only the necessary response fields and HTTP status code for technical explanation.
- [ ] Verify each redacted output as saved, not merely the original editor preview.
- [ ] Inspect file metadata and exclude raw Burp project files and unredacted exports.
- [ ] Only publish evidence once the review is complete.

Never upload live credentials or copy authentic tokens into text documentation. Prefer new short-lived lab sessions when creating publication-ready screenshots.
