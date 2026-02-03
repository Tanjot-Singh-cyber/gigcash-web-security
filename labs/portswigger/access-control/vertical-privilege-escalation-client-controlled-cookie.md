# Lab: User role controlled by request parameter

## Vulnerability Type
Broken Access Control – Vertical Privilege Escalation

## Summary
The application determines whether a user is an administrator based on a client-controlled cookie. By modifying this cookie, a normal user can gain access to admin-only functionality.

## Technical Details
- The application sets an `Admin=false` cookie for normal users.
- The backend trusts the value of this cookie when authorizing access.
- There is no server-side validation of the user’s role tied to the session.
- Changing the cookie to `Admin=true` grants administrative privileges.

## Exploitation Steps
1. Log in as a normal user.
2. Intercept a request and observe the cookie: Admin=false
3. Modify the cookie to: Admin=true
4. Access the admin panel.
5. Delete the user `carlos`.

## Impact
An attacker can escalate privileges from a normal user to an administrator, gaining full control over administrative functionality.

## Root Cause
Authorization decisions are made using client-controlled data instead of server-side role validation.

