# Access Control – Mental Model

Access control determines whether an authenticated user
is allowed to perform a specific action on a specific resource.

Three components must always be identified:

1. Subject (who)
   - User identity (session, cookie, token)

2. Object (what)
   - Resource identifier (userId, accountId, orderId, fileId)

3. Action (how)
   - Read / Write / Delete / Admin action

A vulnerability exists when:
- The server does not verify that the subject
  is authorized to perform the action on the object.

Common failures:
- Trusting user-supplied IDs
- Enforcing checks only in the UI
- Missing ownership validation
- Role checks applied inconsistently

Key rule:
Authentication ≠ Authorization
