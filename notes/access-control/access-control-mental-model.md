# Access Control Mental Model (IDOR)

## Authentication vs Authorization

- Authentication answers: **Who are you?**
- Authorization answers: **What are you allowed to do?**
- Most applications authenticate correctly but fail authorization.
- IDOR is an authorization failure, not an authentication issue.

---

## Subject / Object / Action Model

Every sensitive request must be evaluated using three elements:

### Subject
- The user making the request
- Identified via session cookie, JWT, or token

### Object
- The resource being accessed
- Examples: user profile, order, invoice, ticket, document

### Action
- The operation performed on the object
- Examples: read, update, delete, approve, transfer

Authorization means:
> The server must verify that the **Subject is allowed to perform the Action on the Object**.

If any check is missing → vulnerability.

---

## What IDOR Really Is

- IDOR occurs when the server **trusts a user-controlled reference** to an object
- The reference can be:
  - Numeric ID
  - UUID
  - Email
  - Filename
  - Hidden form field
  - JSON parameter

The format of the reference does not matter.  
**Lack of ownership or permission checks is the real bug.**

---

## Why Cookies Don’t Prevent IDOR

- Cookies only prove the request comes from a logged-in user
- They do NOT prove:
  - The object belongs to the user
  - The user has permission to access or modify it
- Authorization must always be enforced server-side per object

---

## Common Vulnerable Patterns

- “User is logged in, so allow request”
- “Object ID came from the frontend, so it’s safe”
- “This endpoint isn’t linked in the UI”
- “Users won’t guess other IDs”

All of these assumptions are wrong.

---

## Why IDOR Is Common in Real-World Apps

- Authentication is handled by frameworks
- Authorization is custom business logic
- Business logic is rushed and inconsistently implemented
- Developers focus on UI restrictions instead of server-side checks

This makes IDOR one of the most common and severe real-world vulnerabilities.
