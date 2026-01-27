# CSRF – Technical Notes

## What CSRF Actually Is
Cross‑Site Request Forgery happens when:
- A browser sends an authenticated request
- The user did NOT intend to perform that action
- The server trusts the request only because cookies are present

The browser is the attacker’s delivery mechanism.

---

## Core CSRF Mental Model

### Roles
- **Victim**: Logged‑in user
- **Attacker**: Hosts malicious HTML
- **Browser**: Sends requests automatically with cookies
- **Server**: Cannot distinguish legit vs forged request

---

## Why CSRF Works

1. Browser automatically attaches cookies
2. Server uses cookies for authentication
3. No reliable proof of user intent
4. Cross‑site requests are allowed by default

Cookies ≠ consent.

---

## State‑Changing Actions
Any request that modifies server state:
- Change email
- Change password
- Transfer money
- Delete account

HTTP method is irrelevant.
Only **state change** matters.

---

## POST vs GET (Reality)

### Myth
> POST is secure, GET is unsafe

### Truth
- GET and POST are just transport methods
- Both can change server state
- CSRF can happen on **any method**
- Security must NOT depend on method

If a GET request changes state → it must be protected.

---

## CSRF Tokens (Correct Use)

### What a CSRF Token Does
- Binds a request to a user session
- Must be unpredictable
- Must be validated server‑side

### Correct Validation
- Token checked for **every state‑changing request**
- Token tied to session
- Token cannot be reused cross‑site

### Common Failure
- Token validated only on POST
- Token missing but request still processed
- Token checked client‑side only

---

## Method‑Based CSRF Failure (This Lab)

### What the App Did Wrong
- Enforced CSRF token on POST
- Ignored CSRF protection on other methods
- Assumed state change only happens via POST

### Why This Is Dangerous
Attackers don’t care about methods.
They care about **what the server accepts**.

---

## Attacker POV (Important)

- Attacker does NOT see cookies
- Attacker does NOT see responses
- Attacker only needs the request to be sent
- If server accepts it → attack succeeds

Blind attack.

---

## Browser Behavior (Critical)

- Browser sends cookies automatically
- Browser does NOT ask user for permission
- Browser does NOT care about intent
- Server must enforce protection

---

## SameSite Cookies (Brief)

- `SameSite=None` → cookies sent cross‑site
- `SameSite=Lax` → sent on top‑level GET navigation
- `SameSite=Strict` → not sent cross‑site

SameSite helps, but is NOT a full CSRF solution.

---

## Origin / Referer Checks

- Server checks where request came from
- Fragile (can be missing or stripped)
- Must be a defense‑in‑depth measure

Never rely on this alone.

---

## Interview Lines

- “CSRF is about **forged intent**, not stolen data.”
- “Cookies authenticate users, not actions.”
- “HTTP methods do not define security boundaries.”
- “CSRF defenses must protect **state changes**, not endpoints.”

---

## Final Takeaway

If a server accepts:
- Authenticated request
- Without validating intent
→ CSRF is possible.

Method‑based protection is broken by design.
