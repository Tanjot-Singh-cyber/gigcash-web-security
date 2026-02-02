# IDOR – API Key Disclosure via UUID Reuse

## Lab Summary
The application allows users to access account details using a UUID supplied in the `id` parameter. Although UUIDs are unpredictable, the application leaks other users’ UUIDs in public blog content and fails to enforce authorization checks on sensitive endpoints.

## Vulnerable Endpoint
GET /my-account?id=<UUID>

## Attack Steps
1. Logged in as a normal user.
2. Inspected public blog content and identified a link containing Carlos’s UUID:
   /blogs?userId=e72b8e06-ec4b-4aca-9bb1-fa18d53cb32a
3. Sent the `/my-account` request to Burp Repeater.
4. Replaced my own UUID with Carlos’s UUID in the `id` parameter.
5. Server returned Carlos’s private account page.
6. Extracted Carlos’s API key from the response.

## Impact
- Unauthorized access to another user’s account
- Disclosure of sensitive credentials (API key)
- Full account compromise potential

## Root Cause
- Missing server-side authorization checks
- Reliance on UUID secrecy instead of access control
- Leakage of object references in public content

## Key Takeaway
Unpredictable identifiers do not prevent IDOR if authorization is not enforced. Any user-supplied identifier must be validated against the authenticated user’s permissions.
