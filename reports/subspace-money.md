# Security Assessment Report — subspace.money

| Field | Details |
|---|---|
| **Target** | subspace.money |
| **Researcher** | Tanjot Singh |
| **Date** | April 20, 2026 |
| **Severity** | 🔴 High |
| **Status** | Reported — Pending Triage |

---

## Executive Summary

During a security review of `subspace.money`, I identified a potential High severity vulnerability in the GraphQL API. The `getBankDetails` query appears to lack server-side authorization when fetching bank details.

If confirmed, this allows any authenticated user to query another user's financial data — including bank account numbers, IFSC codes, and UPI IDs — by supplying a known `user_id` UUID.

---

## Vulnerability Details

| Field | Details |
|---|---|
| **Type** | Insecure Direct Object Reference (IDOR) / Broken Object Level Authorization (BOLA) |
| **Endpoint** | `db.subspace.money/v1/graphql` |
| **Affected Query** | `getBankDetails` |
| **Parameter** | `user_id` (UUID, supplied in request variables) |

### Description

The `getBankDetails` query retrieves sensitive financial data using a `user_id` parameter passed directly in the request body. The server does not validate whether the requesting user owns the supplied `user_id` — it processes the request regardless of session ownership.

---

## Proof of Concept

### Steps to Reproduce

**Step 1 — Authenticate**
Log into the application and start traffic capture via Burp Suite.

**Step 2 — Trigger the endpoint**
Navigate to `/profile/payment` to trigger the `getBankDetails` query.

**Step 3 — Identify your own `user_id`**
Observe the `user_id` in the GraphQL request variables in Burp Suite.

**Step 4 — Obtain a target UUID**
The `GetFriendInfo` query — exposed during standard chat interactions — returns other users' UUIDs. No special privilege required.

**Step 5 — Modify and replay**
In Burp Repeater, replace your `user_id` with the target's UUID and resend the request.

**Step 6 — Observe the response**

```json
HTTP/1.1 200 OK

{
  "data": {
    "whatsub_bank_account_details": []
  }
}
```

### Expected vs Actual Behavior

| | Behavior |
|---|---|
| **Expected** | Server compares session token with requested `user_id`. Returns `403 Forbidden` if mismatch. |
| **Actual** | Server returns `200 OK` with an empty array. No authorization error. No ownership check. |

---

## Attack Chain

```
Authenticated attacker
        │
        ▼
GetFriendInfo query (chat feature)
        │  exposes target UUID
        ▼
getBankDetails query
        │  user_id replaced with target UUID
        ▼
200 OK — no authorization check
        │
        ▼
Bank account number, IFSC code, UPI ID exposed
```

---

## Impact

The empty response is likely due to the test account having no saved bank data — **not** an access denial. The absence of a `403` or authorization error strongly indicates the logic flaw exists regardless of data presence.

If an attacker targets a user with a completed bank profile:
- Bank account number exposed
- IFSC code exposed
- UPI ID exposed

Because UUIDs are accessible through standard app features (`GetFriendInfo`), the barrier to exploitation is low. This creates a viable financial data scraping attack chain against any user on the platform.

---

## Testing Limitations

I tested with a limited number of accounts. The target UUID used belonged to a support team account with no bank data stored. I was not able to confirm live data extraction.

However, the server's failure to return an authorization error — rather than an empty dataset — is the indicator of the missing access control, not the data itself.

---

## Remediation

The backend should derive `user_id` from the authenticated session or JWT — never from the request body.

```python
# Vulnerable pattern
user_id = request.variables.get("user_id")
bank_details = db.query(user_id)

# Secure pattern
user_id = session.get_authenticated_user_id()
bank_details = db.query(user_id)
```

The client should never control which user's data is returned. Session ownership must be the sole source of truth.

---

## References

- [OWASP — Broken Object Level Authorization (BOLA)](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
- [PortSwigger — Insecure Direct Object References](https://portswigger.net/web-security/access-control/idor)

---

*This report was submitted to the subspace.money security team via Bugbase on Day 52 of my real-application security testing program.*
