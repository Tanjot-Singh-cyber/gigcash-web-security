# CSRF token bypass via GET request

## Lab
CSRF vulnerability with token validation enforced only on POST requests.

## Vulnerability
The application validates CSRF tokens for POST requests but does not protect state-changing actions when accessed via GET.

## Exploitation
An attacker can craft a malicious HTML page that triggers a GET request to a sensitive endpoint.  
The victim’s browser automatically includes the session cookie, and the server processes the request without CSRF validation.

## Impact
Attacker can perform unauthorized state-changing actions (e.g., change email) on behalf of an authenticated victim.

## Status
✅ Lab solved
