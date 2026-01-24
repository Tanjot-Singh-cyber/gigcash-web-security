# CSRF with No Defenses

## Lab
CSRF vulnerability with no defenses

---

## State-changing action
POST /my-account/change-email  
Changes the user’s email address.

---

## Authentication mechanism
Cookie-based session authentication.

Browser automatically attaches the session cookie to requests.

---

## CSRF protection check
- No CSRF token in request
- No secret tied to user intent

---

## Why this is vulnerable
Because authentication relies only on cookies, another website can trigger the same POST request using an auto-submitting HTML form.

---

## Exploit logic (conceptual)
A malicious page can contain a form that submits:
- action → /my-account/change-email
- method → POST
- parameter → email=attacker@evil.com

When a logged-in victim visits the page, the browser sends the request with the victim’s session cookie.

---

## Key takeaway
CSRF happens when the application verifies *who* the user is, but not *whether they intended* the action.
