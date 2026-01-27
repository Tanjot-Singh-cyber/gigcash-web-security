# CSRF where token validation depends on request method

## Lab Goal
Exploit a CSRF vulnerability where the application validates the CSRF token only for POST requests, but allows state-changing actions via other HTTP methods.

## Application Behavior
- Email change endpoint: `/my-account/change-email`
- CSRF token is present and validated for POST requests
- Cookies are used for authentication
- Server incorrectly assumes state change only happens via POST

## Key Observation
The server does **not enforce CSRF protection consistently across HTTP methods**.
If a state-changing request can be triggered without proper token validation, CSRF becomes possible.

## Attack Idea (High-level)
- Attacker hosts a malicious page
- Victim visits attacker-controlled site while logged in
- Victim’s browser automatically attaches session cookies
- Browser sends a request that the server accepts without validating CSRF token properly

## Security Impact
An attacker can force authenticated users to perform unintended state-changing actions (e.g., email change) without user consent.

## Root Cause
- CSRF protection tied to HTTP method instead of actual state change
- Trusting POST as “safe” and ignoring other methods

## Takeaway
CSRF defenses must be applied based on **state-changing behavior**, not request method.
