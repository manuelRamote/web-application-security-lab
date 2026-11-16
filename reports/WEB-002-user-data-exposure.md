# WEB-002 — Potential excessive exposure of user attributes

**Status:** Observed; nature of `password` value and security impact not established  
**Affected endpoint:** `GET /rest/saveLoginIp`  
**Relevant category:** Excessive response data / object property exposure  
**Severity:** Pending assessment

## Observations

With an authenticated lab account, `GET /rest/saveLoginIp` returned a user JSON object with fields including `id`, `email`, `role`, `password`, `totpSecret`, `lastLoginIp`, and `isActive`.

- The `password` property held a **nonempty value** in the observed responses. Its value was redacted and its representation (e.g., hash or other) **was not verified**.
- The `totpSecret` and `lastLoginIp` fields were empty strings in the reviewed example.
- A request to the same endpoint **without** authentication headers or cookies returned **`401 Unauthorized`**.
- Separate lab users A and B received their own user records when authenticated.

## Assessment

Including a `password` property in an API response warrants review even when returned to an authenticated account. If the property is a password hash or another sensitive credential artifact, its transmission to a browser may represent unnecessary exposure. However, the screenshots do **not** prove plaintext credential disclosure, access by an anonymous requester or access to another user's record.

**This is a potential data-minimization weakness, not yet a confirmed high-impact vulnerability.**

## Recommended remediation / next checks

- Return an explicit allowlist of fields needed by the client, excluding password representations and authentication secrets.
- Verify the exact serialized value type and intended client use without copying secrets into reports.
- Keep the endpoint protected by authentication and independently evaluate horizontal authorization where appropriate.

## Evidence status

Awaiting sanitized authenticated response screenshot(s). Do not upload session tokens, cookies, email addresses, hashes or TOTP secrets.
