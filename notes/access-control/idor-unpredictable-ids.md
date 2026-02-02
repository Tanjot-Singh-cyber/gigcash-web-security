# IDOR with Unpredictable Identifiers (UUIDs)

## Core Idea
Using UUIDs instead of sequential IDs does NOT prevent IDOR vulnerabilities. Security must rely on authorization checks, not identifier secrecy.

## Common Developer Mistake
Assuming:
- “Users can’t guess UUIDs, so access is safe”

Reality:
- UUIDs are often leaked via:
  - URLs
  - HTML source
  - API responses
  - Public content (blogs, comments, profiles)

## Mental Model
Access control must answer:
- Who is the user?
- What object are they requesting?
- Are they allowed to access THIS object?

If the server does not enforce this → IDOR.

## Typical Exploitation Pattern
1. Identify an endpoint that accepts an object identifier (id, userId, uuid).
2. Find a way to obtain another user’s identifier.
3. Reuse that identifier in a sensitive endpoint.
4. Server returns unauthorized data due to missing checks.

## Example
GET /my-account?id=<UUID>

If the server does not verify ownership:
- Horizontal privilege escalation (other users)
- Vertical privilege escalation (admin)

## Security Principle
Authorization must be:
- Server-side
- Context-aware
- Independent of identifier complexity

## One-Line Explanation (Interview-Ready)
UUIDs prevent enumeration, not authorization failures.
