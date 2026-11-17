# WEB-005 — Basket quantity manipulation (business logic validation)

**Status:** Confirmed negative-quantity validation weakness in the local training application  
**Category:** Business logic / improper input validation  
**Suggested CWE:** CWE-20 — Improper Input Validation  
**Affected update endpoint:** `PUT /api/BasketItems/9`  
**Verification endpoint:** `GET /rest/basket/6`  
**Tools:** Burp Suite Community Edition (Proxy, Repeater), OWASP Juice Shop browser UI  
**Test date:** 17 November 2026  
**Severity:** Not formally scored; checkout/payment impact was not tested

## Summary

An authenticated lab user changed a basket item's quantity using Burp Suite Repeater. A normal `quantity: 2` update succeeded. When the value was changed to `-1`, the application returned `200 OK` with the negative quantity, retained that state in a subsequent `GET /rest/basket/6`, and displayed a **negative basket total** in the user interface.

A second boundary test (`quantity: 0`) was also accepted and displayed as a zero-quantity item with a zero total. That behavior is documented but **is not independently classified as a vulnerability**, because the intended treatment of zero quantities has not been established.

The basket was then restored to **two units** and a positive total of **3.98** using the same local test account. All tests involved an intentionally vulnerable, locally hosted training target and the operator's own account.

## Test scope and identifiers

| Element | Value |
| --- | --- |
| Application | OWASP Juice Shop |
| Target origin | `http://127.0.0.1:3000` |
| Test account ID | `26` |
| Basket ID | `6` |
| Basket item ID | `9` |
| Product ID | `6` — Banana Juice (1000 ml) |
| Observed unit price | `1.99` |

The account, basket and product IDs are specific to this local test. They are not generalized into universal identifiers for other Juice Shop installations.

## Reproduction and observations

### 1. Normal update (control)

After adding the product to the basket, updating the item quantity from 1 to 2 generated a successful request to:

```http
PUT /api/BasketItems/9 HTTP/1.1
Content-Type: application/json

{"quantity":2}
```

The server returned `200 OK` with `quantity: 2`. A separate `GET /rest/basket/6` also returned a basket item with quantity 2.

### 2. Negative quantity — confirmed weakness

A request with the same authentication and resource identifier but a negative quantity was submitted:

```http
PUT /api/BasketItems/9 HTTP/1.1
Content-Type: application/json

{"quantity":-1}
```

**Observed:**

- Response was `HTTP 200 OK` with `status: "success"` and `quantity: -1`.
- A separate `GET /rest/basket/6` returned the same basket item (`id: 9`) with **`quantity: -1`**, confirming that the invalid state was retained.
- The **Your Basket** interface displayed `-1` unit and a **total of -1.99**.

These three observations support the finding that the backend does not reject negative quantities at the tested update endpoint and that the invalid data affects the basket's displayed total.

### 3. Zero quantity — boundary behavior

A further update sent:

```json
{"quantity":0}
```

The server returned `HTTP 200 OK` and `quantity: 0`. The UI continued to display the product with quantity `0` and a total of `0`, rather than removing it from the basket. This was observed in the request response and on-screen UI; **a separate post-update GET for the zero case has not been evidenced**, so persistence is not asserted separately for this boundary case.

Whether zero quantity should remove the item is a product-specific business rule and is not conclusively a security flaw.

### 4. Restore test state

To conclude the test, the operator sent:

```json
{"quantity":2}
```

The server returned `HTTP 200 OK` with `quantity: 2`. The browser basket displayed two units and a total of `3.98`. This confirms the local UI state was restored to a positive quantity.

## Observed impact and limitations

**Confirmed:** unauthorized-by-business-rule state (negative item quantity) can be introduced through the authenticated update endpoint and later retrieved. The application renders a negative basket total.

**Not tested or claimed:** completing an order for a negative amount, payment processor manipulation, account credits, refund issuance, price database modification, cross-user basket modification, authentication bypass, or persistence across a service restart.

A successful `PUT` by the item owner does **not** indicate a broken authorization control; the issue under examination is **server-side validation of the quantity value**.

## Recommended remediation

1. Validate integer quantity values **server-side** before creating or updating basket items. Reject negative values with a documented client error (typically `400 Bad Request`).
2. Define explicit business semantics for zero: reject zero or remove the item atomically as appropriate; never allow an inconsistent UI/data state.
3. Enforce sensible upper bounds and data types; validate both API input and any alternative quantity-update routes.
4. Recompute pricing and totals on the trusted backend, with independent validation before checkout.
5. Add regression tests for `-1`, `0`, valid positive integers, non-integers, extremely large values and repeated update requests.
6. Avoid revealing internal validation details in client responses; retain relevant logs for diagnosis.

## Evidence — prepared locally, upload pending

Seven images derived from the original Burp and browser screenshots have been prepared, with authentication-bearing request headers excluded from Burp excerpts and test-account email removed from basket UI screenshots.

| Planned image | Demonstrates |
| --- | --- |
| `14-negative-quantity-put.png` | `PUT` with `quantity: -1`; server accepts it |
| `15-negative-total-ui.png` | `-1` unit and `-1.99` total shown |
| `16-negative-quantity-persisted.png` | Later `GET /rest/basket/6` shows `quantity: -1` |
| `17-zero-quantity-put.png` | `PUT` with `quantity: 0`; server accepts it |
| `18-zero-total-ui.png` | Zero-quantity product remains visible |
| `19-restored-quantity-put.png` | `PUT` restores quantity 2 |
| `20-restored-basket-ui.png` | Basket shows two units, total 3.98 |

**The images are not yet in GitHub and therefore are not embedded here.** After the user uploads and checks them, link each verified image and update the evidence index. The Burp images are excerpts taken from the **same real request and response** with sensitive middle headers omitted; they are not full raw HTTP requests. The underlying request/response values have not been edited or invented.

See the [evidence inventory](../evidence/README.md) and retain the repository as **private** until a final privacy review is complete.
