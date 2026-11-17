# Evidence — Upload and redaction checklist

**Current status:** **Thirteen screenshots** have been uploaded and linked in the private repository: WEB-001 (five), WEB-002 (two), WEB-003 (two), and WEB-004 (four). A final privacy review is required before changing repository visibility.

## Evidence inventory and upload status

| File | Evidence | Status |
| --- | --- | --- |
| [`01-http-history-baseline.png`](01-http-history-baseline.png) | Juice Shop request/response in Burp Proxy | Uploaded and linked |
| [`02-repeater-search-normal.png`](02-repeater-search-normal.png) | Normal `q=apple` search | Uploaded and linked |
| [`03-sqli-sqlite-error.png`](03-sqli-sqlite-error.png) | Malformed input and SQLite error | Uploaded and linked |
| [`04-sqli-true-condition.png`](04-sqli-true-condition.png) | True-condition SQL injection test | Uploaded and linked |
| [`05-sqli-false-condition.png`](05-sqli-false-condition.png) | False-condition SQL injection test | Uploaded and linked |
| [`06-authenticated-user-response.png`](06-authenticated-user-response.png) | Authenticated user property names; authorization and sensitive values redacted | Uploaded and linked |
| [`07-unauthenticated-401.png`](07-unauthenticated-401.png) | `/rest/saveLoginIp` rejected without authentication | Uploaded and linked |
| [`08-cross-user-address-rejected.png`](08-cross-user-address-rejected.png) | User A received HTTP 400 when requesting B's address; request headers omitted | Uploaded and linked |
| [`09-owner-address-allowed.png`](09-owner-address-allowed.png) | User B received HTTP 200 for own address; request headers and contact data omitted | Uploaded and linked |

### WEB-004 — XSS evidence

| Screenshot | What it shows | Status |
| --- | --- | --- |
| [`10-dom-html-rendering.png`](10-dom-html-rendering.png) | Benign HTML is inserted as an element inside `span#searchValue` | Uploaded and linked |
| [`11-dom-xss-execution.png`](11-dom-xss-execution.png) | JavaScript alert `WEB004` and `<img onerror>` in DOM | Uploaded and linked |
| [`12-dom-xss-repeat.png`](12-dom-xss-repeat.png) | Repeated execution produces the same alert | Uploaded and linked |
| [`13-burp-repeater-api-baseline.png`](13-burp-repeater-api-baseline.png) | Burp `q=LABXSS2026` request and `200 OK`, `data: []` response | Uploaded and linked |

The Burp baseline is supplementary: it documents ordinary API behavior, not proof of DOM XSS. The browser-side execution and DOM insertion are documented in images 10–12. The new Repeater screenshot uses cropped excerpts of the **same real request/response**; the portion of the request containing authorization and cookie headers has been removed, not blurred. Its upload and path have been confirmed in the repository tree.

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
