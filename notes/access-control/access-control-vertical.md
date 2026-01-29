# Vertical Access Control – Unprotected Admin Functionality

## Core Idea
Vertical access control failures occur when a server does NOT enforce authorization checks for privileged functionality (e.g., admin-only actions).

Hiding functionality in the UI, JavaScript, or robots.txt is NOT a security control.

---

## Key Mental Model
Access control is a SERVER-SIDE decision.

The only question that matters:
> Does the server verify the user’s role before performing the action?

If the answer is NO → vulnerability exists.

---

## Discovery vs Root Cause
- robots.txt revealing `/administrator-panel` is NOT the vulnerability.
- The vulnerability exists because:
  - The admin endpoint is accessible directly
  - No authorization check is enforced on the server

robots.txt is only a discovery mechanism, not a security failure.

---

## Why robots.txt Is Not Security
robots.txt:
- Is publicly accessible
- Is meant for search engines, not access control
- Can be ignored by attackers

Security through obscurity ≠ security.

---

## Attack Flow
1. Attacker identifies a potentially privileged endpoint (e.g. `/administrator-panel`)
2. Attacker accesses it directly (forced browsing)
3. Server responds without checking user role
4. Privileged action is executed (e.g. deleting users)

---

## Real-World Takeaway
Never trust:
- Hidden links
- Missing buttons
- Client-side restrictions

Always enforce authorization checks on the server.

---

## One-Line Explanation (Interview Safe)
Hiding admin functionality is not a security control because authorization must be enforced server-side, otherwise any user can access privileged endpoints via direct requests.
