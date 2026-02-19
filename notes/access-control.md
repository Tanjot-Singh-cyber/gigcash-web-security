# Access Control – Master Mental Model (IDOR & Privilege Escalation)

## 1. Authentication vs Authorization (Non-Negotiable)
- **Authentication** answers: *Who are you?*
- **Authorization** answers: *What are you allowed to do?*
- Most real-world failures are **authorization bugs**, not auth bugs.
- IDOR is **never** an authentication issue.

Key rule:
> Authentication ≠ Authorization

---

## 2. Subject – Object – Action Model (Core Framework)

Every sensitive request must be evaluated server-side using **three components**:

### Subject (Who)
- The authenticated user
- Identified via session cookie, JWT, or token

### Object (What)
- The resource being accessed
- Examples: `userId`, `orderId`, `accountId`, `fileId`, `uuid`

### Action (How)
- Operation performed on the object
- Examples: read, update, delete, approve, transfer, admin action

Authorization means:
> The server must verify that the **Subject is allowed to perform the Action on the Object**.

If **any** of these checks is missing → vulnerability exists.

---

## 3. What IDOR Really Is
IDOR occurs when the server:
- Accepts a **user-controlled object reference**
- Fails to verify ownership or permission

The reference can be:
- Numeric ID
- UUID
- Email
- Filename
- Hidden form field
- JSON parameter

Important:
> The **format of the identifier does not matter**.  
> Missing authorization checks are the real bug.

---

## 4. Why Cookies / Sessions Do NOT Prevent IDOR
Cookies and sessions only prove:
- The request comes from a logged-in user

They do **NOT** prove:
- The object belongs to the user
- The user is allowed to access or modify it

Authorization must be enforced:
- Server-side
- Per request
- Per object

---

## 5. Common Vulnerable Assumptions (All Wrong)
- “User is logged in, so allow it”
- “Object ID came from the frontend”
- “This endpoint isn’t linked in the UI”
- “Users won’t guess other IDs”
- “We use UUIDs, so it’s safe”

Security based on assumptions → broken access control.

---

## 6. Horizontal vs Vertical Access Control

### Horizontal Access Control (IDOR)
- User accesses **another user’s data**
- Same role, different object
- Example: `userA` accessing `userB`’s order

### Vertical Access Control
- User accesses **privileged functionality**
- Role escalation (user → admin)
- Example: normal user accessing `/admin/deleteUser`

Both are authorization failures.

---

## 7. Unprotected Admin Functionality (Vertical)

### Core Idea
Hiding admin features is **not** access control.

Access control is a **SERVER-SIDE decision**.

The only question that matters:
> Does the server verify the user’s role before performing the action?

If NO → vulnerability exists.

---

## 8. robots.txt, Hidden Links, UI Checks (Not Security)

- `robots.txt` is public
- JavaScript restrictions are client-side
- Hidden buttons ≠ protection

Important distinction:
- Discovery mechanism ≠ root cause

The vulnerability exists because:
- The server executes privileged actions
- Without enforcing authorization

---

## 9. IDOR with UUIDs (Unpredictable Identifiers)

### Core Idea
UUIDs prevent **enumeration**, not **authorization failures**.

Common developer mistake:
- “Users can’t guess UUIDs, so it’s safe”

Reality:
UUIDs leak via:
- URLs
- API responses
- HTML source
- Logs
- Public content

If ownership is not verified → IDOR still exists.

One-line rule:
> Identifier secrecy is not access control.

---

## 10. Typical Exploitation Workflow
1. Identify endpoint accepting object identifier
2. Obtain another user’s identifier (any source)
3. Replace identifier in request
4. Observe unauthorized access or action

Applies to:
- Read endpoints
- Update/delete actions
- Multi-step workflows
- APIs

---

## 11. Why Access Control Fails in Real Applications
- Authentication handled by frameworks
- Authorization is custom business logic
- Business logic is rushed and inconsistent
- Developers rely on UI restrictions
- Backend trust assumptions go unchecked

This makes access control the **most common real-world vulnerability class**.

---

## 12. Testing Mindset (How to Think)
Always ask:
- What is the current state?
- Who does the server think I am?
- What object am I acting on?
- What assumption is the server making?

If the server trusts **anything** from the client → test it.

---

## 13. Interview-Safe One-Liners
- IDOR is an authorization failure caused by missing ownership checks.
- UUIDs prevent guessing, not authorization bugs.
- Hiding functionality is not a security control.
- Access control must be enforced server-side per object and action.
