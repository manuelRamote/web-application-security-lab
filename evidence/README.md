# Evidence — Upload and redaction checklist

**Current status:** Evidence images are **not yet uploaded**. The initial repository includes documentation only.

## Proposed naming convention

| File (planned) | Evidence |
| --- | --- |
| `01-http-history-baseline.png` | Juice Shop request/response in Burp Proxy |
| `02-repeater-search-normal.png` | Normal `q=apple` search |
| `03-sqli-sqlite-error.png` | Malformed input and SQLite error |
| `04-sqli-true-condition.png` | True-condition SQL injection test |
| `05-sqli-false-condition.png` | False-condition SQL injection test |
| `06-authenticated-user-response.png` | Authenticated user property names, secrets redacted |
| `07-unauthenticated-401.png` | `/rest/saveLoginIp` rejected without authentication |
| `08-cross-user-address-rejected.png` | User A blocked from B's address |
| `09-owner-address-allowed.png` | User B successfully accesses own address |

## Privacy checklist

- [ ] Obscure **all** JWTs, Authorization headers, `token` cookies, session identifiers and any secret-bearing headers. Do not use reversible pixelation for actual credentials.
- [ ] Obscure emails, phone numbers, physical address details and passwords/password hashes, even when fictional.
- [ ] Ensure browser tabs, URL parameters, desktop paths and terminal history contain no unrelated private information.
- [ ] Include only the necessary response fields and HTTP status code for technical explanation.
- [ ] Verify each redacted output as saved, not merely the original editor preview.
- [ ] Inspect file metadata and exclude raw Burp project files and unredacted exports.
- [ ] Only publish evidence once the review is complete.

Never upload live credentials or copy authentic tokens into text documentation. Prefer new short-lived lab sessions when creating publication-ready screenshots.
