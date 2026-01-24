# CSRF – Core Mental Model 

## What CSRF actually is
CSRF happens when a server performs a sensitive action because it receives
a valid authenticated request, without verifying that the request was
intentionally initiated by its own application.

The vulnerability exists because:
- browsers automatically attach authentication cookies
- servers assume "authenticated request = user intent"

This assumption is wrong.

---

## Canonical CSRF flow

1. User logs in to target site (e.g. bank.com)
2. Server sets a session cookie in the browser
3. User later visits an attacker-controlled site (evil.com)
4. Evil site causes the browser to send a request to bank.com
   (via form, image, iframe, etc.)
5. Browser automatically includes the session cookie
6. Server receives a valid authenticated request
7. Server executes the action

No password is entered.
No UI is used.
No response is read by the attacker.

---

## Key clarification

- The attacker’s server does NOT send requests to the victim server
- The victim’s BROWSER sends the forged request
- This is browser behavior by design, not a browser bug

CSRF exploits server-side trust in browser automation.

---

## What CSRF is NOT

- Not phishing
- Not stealing credentials
- Not breaking authentication
- Not trusting responses

CSRF abuses existing authentication.

---

## Typical CSRF targets

- Change email
- Change password (without re-auth)
- Transfer funds
- Add shipping address
- Enable attacker-controlled 2FA
- Perform admin actions

Any action that relies only on session cookies is a candidate.

---

## One-line definition (lock-in)

CSRF occurs because the server trusts the browser to send authenticated
requests only when the user intends them.

---

## Relation to XSS

- XSS: attacker executes code inside the site
- CSRF: attacker abuses the browser from outside the site

Different threat models. Different defenses.
