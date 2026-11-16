# WEB-003 — Horizontal access control: address lookup

**Status:** Tested; no cross-user read access observed in this scenario  
**Category:** Authorization validation (not a confirmed vulnerability)  
**Endpoint:** `GET /api/Addresss/7` (spelling as observed)

## Test setup

Two accounts controlled by the lab operator:

- **User A:** ID `26`
- **User B:** ID `27`, owner of address ID `7`

The test address used fictional contact information. The address creation request `POST /api/Addresss/` returned `201 Created`, with address ID `7` associated with user ID `27`. The owner's list endpoint `GET /api/Addresss` returned the same association.

## Results

| Test | Observation |
| --- | --- |
| User A requesting `/api/Addresss/7` | `400 Bad Request`; application message `Malicious activity detected.` |
| User B requesting `/api/Addresss/7` with a valid B session | `200 OK`; address 7 returned with `UserId: 27` |

An earlier attempt using the supposed B context also returned `400`; after rechecking the session and making a new owner request, `200` and the expected owned record were observed. The earlier failed attempt is **not** represented as proof of a vulnerability or an enforcement control by itself.

## Interpretation

This controlled comparison is **consistent with** authorization restricting the tested single-address read to the owner. The experiment did not demonstrate an IDOR in this endpoint. It is not a comprehensive test of all address operations, routes or resource identifiers.

## Evidence status

Screenshots from Burp have not been uploaded. Sanitize all Authorization headers, cookies and fictional contact data before publication.
