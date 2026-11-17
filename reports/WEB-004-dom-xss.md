# WEB-004 — DOM-based Cross-Site Scripting in product search

**Status:** Confirmed and reproduced in the local training environment  
**Category:** Cross-Site Scripting (DOM-based XSS)  
**CWE:** CWE-79 — Improper Neutralization of Input During Web Page Generation  
**Affected UI route:** `/#/search?q=...`  
**DOM element:** `span#searchValue`  
**Tools:** Burp Suite integrated browser; Chromium Developer Tools (Elements)  
**Severity:** Not formally scored; only harmless JavaScript execution was tested  
**Test date:** 17 November 2026

## Summary

The OWASP Juice Shop search view reads a user-controlled term supplied in the **URL fragment** and renders it into the browser DOM. In the tested local instance, HTML markup was interpreted as elements rather than displayed solely as text. A controlled event-handler payload executed JavaScript, as demonstrated by a browser alert containing `WEB004`.

This is a **confirmed DOM-based XSS in the local intentionally vulnerable training application**. The root-cause code path has not been independently audited, so no specific framework binding or source-code function is asserted here.

## Baseline and confirmation tests

| Step | Input | Observed result |
| --- | --- | --- |
| Ordinary search | `LABXSS2026` | Marker rendered in the search-result heading; no matching products |
| HTML interpretation check | `<b>LABXSS2026</b>` | A real `<b>` child appeared inside `span#searchValue`; text rendered bold |
| JavaScript execution check | `<img src=x onerror=alert('WEB004')>` | Browser displayed alert `WEB004`; DevTools showed an `<img>` with `onerror` inside `span#searchValue` |
| Repeatability | Same harmless `<img>` event-handler test | Browser displayed alert `WEB004` again |

### Payload used

```html
<img src=x onerror=alert('WEB004')>
```

The `src=x` image fails to load; the injected `onerror` handler executes the harmless alert when the image error event fires.

## Why the finding is DOM-based

The source is the search parameter in `/#/search?q=...`, which is part of the **URL fragment** after `#`. Fragments are not sent to the server as part of the HTTP request target. In the screenshot, the page DOM includes a generated child element under:

```html
<span id="searchValue">
  <img src="x" onerror="alert('WEB004')">
</span>
```

The direct combination of user-controlled fragment input, DOM element creation, and local browser execution supports a DOM-based XSS classification. This proof does not require or establish a server-side reflected-XSS flaw.

## Demonstrated security impact and limits

**Demonstrated:** JavaScript provided as the search input executes in the browser context of the local Juice Shop origin.

**Not tested or demonstrated:** token theft, credential access, stored/persistent XSS, session hijacking, exploitation against another account, or any access to a real third-party application.

The tests used an isolated local training instance at `127.0.0.1:3000` and a harmless `alert()` marker only.

## Recommended remediation

1. Render untrusted search terms as plain text (for example, with safe text bindings or `textContent`), **not** by inserting untrusted markup into HTML.
2. Avoid unsafe DOM sinks such as unsanitized `innerHTML`; when rich HTML is genuinely required, apply context-appropriate, maintained sanitization with an allowlist that removes event handlers.
3. Apply a restrictive Content Security Policy as defense in depth, recognizing that CSP is not a replacement for proper output handling.
4. Add automated regression tests for encoded HTML tags and event-handler attributes passed through URL fragments.
5. Verify that the search view continues to display harmless terms normally after the fix.

## Evidence status

Three **real screenshots** from 17 November 2026 have been prepared locally, with no custom banners and image metadata stripped. They are **not yet uploaded to GitHub**:

- `10-dom-html-rendering.png` — benign HTML interpretation observed in the search-results element
- `11-dom-xss-execution.png` — initial `WEB004` alert and the associated DOM element in Developer Tools
- `12-dom-xss-repeat.png` — independent repetition producing the same alert

After review and upload, link the images from this report and the [evidence index](../evidence/README.md). Do not upload unrelated authenticated traffic, cookies, tokens or other browser content.
