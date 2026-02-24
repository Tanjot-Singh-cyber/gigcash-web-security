# CSRF – Master Mental Model (Intent, Trust, and State)

## 1. What CSRF Actually Is (Lock This In)

Cross-Site Request Forgery occurs when:
- A browser sends an authenticated request
- The user did **not intend** to perform that action
- The server trusts the request **only because cookies are present**

CSRF is **not** about stealing data.  
CSRF is about **forged user intent**.

Key rule:
> Cookies authenticate users, not actions.

---

## 2. Why CSRF Exists (Root Cause)

CSRF exists because of **two design facts**:

1. Browsers automatically attach cookies to requests
2. Servers assume: authenticated request = user intent

This assumption is wrong.

The browser is not malicious — it is the attacker’s delivery mechanism.

---

## 3. Canonical CSRF Flow (Mental Picture)

1. User logs in to `target.com`
2. Server sets a session cookie
3. User later visits `attacker.com`
4. Attacker page causes browser to send a request to `target.com`
5. Browser automatically includes cookies
6. Server receives a valid authenticated request
7. Server performs the action

No password.
No UI.
No response reading.

The attack succeeds if the server does **not verify intent**.

---

## 4. State Is What Matters (Not Endpoints)

CSRF only matters for **state-changing actions**.

Examples:
- Change email
- Change password
- Transfer funds
- Delete account
- Enable attacker-controlled settings

Important:
- HTTP method is irrelevant
- GET vs POST does **not** define security
- If state changes → it must be protected

---

## 5. POST Is NOT CSRF-Safe (Common Myth)

### Myth
> POST requests are protected against CSRF

### Reality
- Browsers can send cross-site POST requests
- Attackers don’t care about methods
- They care about what the server accepts

If a GET request changes state → CSRF risk  
If a POST request changes state → CSRF risk

Method ≠ intent.

---

## 6. Core CSRF Mental Model (Trust View)

### Trusted Value (Bug)
- Presence of session cookie

### Missing Validation
- Proof that the request was intentionally initiated by the application

CSRF is a **trust failure**, not an input injection.

---

## 7. CSRF Tokens (Primary Defense)

### What CSRF Tokens Prove
- The request originated from the legitimate application
- The user’s browser had access to a secret value the attacker cannot read

### Correct Properties
- Unpredictable
- Tied to the user session
- Validated server-side
- Checked on **every state-changing request**

### Common Implementation Failures
- Static or reusable tokens
- Token validated only on POST
- Token missing but request still processed
- Token checked client-side only
- Token exposed via XSS or URLs

A token that is not validated server-side is meaningless.

---

## 8. SameSite Cookies (What They Really Do)

SameSite controls **when cookies are sent**, not user intent.

Modes:
- `Strict` → no cross-site requests
- `Lax` → cookies sent on top-level GET navigations
- `None` → cookies always sent (with Secure)

Important:
- SameSite reduces attack surface
- SameSite does NOT prove intent
- Legacy browsers and edge cases exist

SameSite is **defense-in-depth**, not a full solution.

---

## 9. Origin / Referer Header Checks

### Why They Exist
- Indicate where the request originated

### Why They’re Brittle
- Headers may be missing
- Headers may be stripped
- “Allow if missing” logic breaks security

These checks:
- Can help
- Must never be relied on alone

---

## 10. Attacker Perspective (Critical)

- Attacker cannot read cookies
- Attacker cannot read responses
- Attacker only needs the request to be sent

CSRF is a **blind attack**.

If the server accepts the request → the attack succeeds.

---

## 11. Relationship Between CSRF and XSS

- CSRF assumes **no XSS**
- XSS allows attackers to:
  - Read CSRF tokens
  - Bypass SameSite
  - Forge requests with full control

Rule:
> Fix XSS first. XSS breaks most CSRF defenses.

---

## 12. Unified Defense Thinking

CSRF defenses attempt to prove **one thing**:
> This request was intentionally initiated by this application.

Layered defenses are strongest:
- CSRF tokens
- SameSite cookies
- Re-authentication for sensitive actions
- Origin checks (secondary)

---

## 13. Interview-Grade One-Liners

- CSRF is about forged intent, not stolen authentication.
- Cookies authenticate users, not actions.
- HTTP methods do not define security boundaries.
- CSRF defenses must protect state changes, not endpoints.

---

## 14. Final Non-Negotiable Rule

If a server performs a sensitive action based only on:
- An authenticated request
- Without validating intent

→ CSRF is possible.
