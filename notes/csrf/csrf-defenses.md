# CSRF Defenses – Reasoning Notes (Day 20)

## 1. The Core CSRF Problem
- What CSRF is in one paragraph
- Authentication vs user intent
- Why browsers enable CSRF by default

## 2. How to Think About CSRF Defenses
- What every CSRF defense is trying to prove
- Why “being logged in” is not enough
- Why state‑changing requests matter

## 3. CSRF Tokens
### What problem they solve
### Why they usually work
### Common implementation mistakes
- Static or reusable tokens
- Token not validated server‑side
- Token exposure via URL or XSS

## 4. SameSite Cookies
### What SameSite actually controls
### Difference between Strict / Lax / None
### Why SameSite is not a complete CSRF defense
- Legacy browsers
- GET requests still allowed
- Cookie protection ≠ intent verification

## 5. Origin and Referer Header Checks
### Why servers rely on these headers
### When they work
### Why they are brittle
- Missing headers
- Stripped or modified headers
- “Allow if missing” logic

## 6. Why POST Requests Are Not CSRF‑Safe
- Common developer misconception
- How browsers can still send POST requests cross‑site
- Method does not equal intent

## 7. Unifying Mental Model
- Browser‑side defenses vs server‑side defenses
- What assumptions each defense makes
- Why layered defenses are stronger

## 8. Relationship Between XSS and CSRF
- Why XSS breaks most CSRF protections
- Why CSRF tokens assume no XSS
- Importance of fixing XSS first


