# CSRF – GET vs POST confusion cleared

## Key Principle
CSRF is not about POST vs GET.
CSRF is about whether the server trusts the request solely based on cookies.

## What happened in lab
- POST requests require a valid CSRF token
- GET requests are not checked
- The same endpoint accepts both methods
- Server assumes GET requests are safe (wrong)

## Why the attack works
1. Victim is logged in → session cookie stored in browser
2. Victim visits attacker-controlled page
3. Malicious HTML auto-submits a GET request
4. Browser attaches cookies automatically
5. Server skips CSRF validation
6. State change occurs

## Important clarifications
- The attacker does NOT send the request
- The victim’s browser sends the request
- The server never knows it came from another site
- Cookies are attached automatically by the browser

## Mental model
If a request:
- changes state
- relies on cookies for auth
- and lacks CSRF validation

→ it is vulnerable to CSRF, regardless of HTTP method
