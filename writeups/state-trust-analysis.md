# Real App State & Trust Analysis

## Target
- Application: mycamu.co.in
- Context: Authenticated student portal
- Action analyzed: Password change from profile

## State Mapping
- State: Authenticated → Password Change
- Identity carrier: Session cookie (connect.sid)
- Additional client-supplied data:
  - Email (JSON body)
  - X-App-Type header

## Sensitive Request (Redacted)
POST /api/login/PwdChangeOnProfile

- Uses session cookie authentication
- Performs credential update

## Trust Boundary Analysis

### Source
- Email field in request body
- Session cookie (connect.sid)

### Trust Assumptions
- Email belongs to the authenticated session user
- Session user is authorized to change this password
- Client-supplied role/type does not affect authorization

### Sink
- Password update in backend user store

## Hypothesis
If the server trusts the Email field over the authenticated session,
a horizontal account takeover may be possible by changing another
student’s password.

## Notes on Potential Trust Chaining
This password change action likely depends on prior authentication
and session integrity. Additional analysis of login, session renewal,
and logout flows may be required to fully assess chained trust assumptions.

## Status
- No exploitation performed
- Hypothesis only (analysis phase)
