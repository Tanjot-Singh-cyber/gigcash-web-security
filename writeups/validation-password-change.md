# Validation of Password Change Trust Controls

## Original Hypothesis
If the server trusts the Email field over the authenticated session,
a horizontal account takeover may be possible.

## Validation Scope
- Application: mycamu.co.in
- Account: own student account only
- Endpoint: POST /api/login/PwdChangeOnProfile
- No exploitation performed

## Baseline Observation
The password change request succeeds when sent as part of the normal
application flow via the browser UI.

## Replay Attempt
Direct replay of the same request in Burp Repeater resulted in:

- HTTP 401 Unauthorized
- Error message: "API is not open"

## Interpretation
This indicates the endpoint is protected by an additional
server-side workflow or state check beyond session authentication.
A valid session cookie alone is insufficient to invoke the action.

## Conclusion
The password change functionality enforces multi-step state validation
and does not accept out-of-context or replayed requests. This reduces
the risk of IDOR, replay, and simple trust-boundary abuse.

## Final Status
- Hypothesis not confirmed
- Control appears to be correctly enforced
- No vulnerability identified in this flow
