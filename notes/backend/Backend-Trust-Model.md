# Backend Trust Model (State · Trust · Revalidation)

## Core Mental Model
Every vulnerability can be reasoned using three questions:

1. What **state** is created or changed?
2. What **value does the server trust later**?
3. Is **ownership / integrity / intent re-validated every time**?

This focuses on reasoning, not payloads.

---

## Example Flow: Email Update

### State
- User email stored in database.

### Trusted Value
- `user_id` / `id` parameter sent by client.

### Trust Point
- Server assumes the `id` belongs to the authenticated user.

### Potential Failure
- If the server does not re-check ownership, attacker may update another user’s email (IDOR).

---

## Generalization Across Vulnerabilities

| Vulnerability Class | State          | Trusted Value | Missing Validation  |
|---------------------|----------------|---------------|---------------------|
| IDOR          | User/Object data    | Object ID      | Ownership          |
| CSRF          | Account action      | Session cookie | User intent        |
| SQL Injection | Query result        | User input     | Query integrity    |
| XSS           | DOM / Page content  | Untrusted data | Context validation |
| Auth Logic    | Login / reset step  | Token / flow   | Flow integrity     |

---

## Key Takeaway
Payloads exploit **broken trust assumptions**.
Finding bugs starts with identifying **what the server trusts incorrectly**.

No exploit attempted without:
- State identification
- Trust point identification
- Revalidation check
