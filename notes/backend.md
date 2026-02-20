# Backend Trust & State Revalidation – Master Mental Model

## Core Principle
Backend vulnerabilities exist because the server **trusts something it should not**  
or **fails to revalidate trust at every request**.

Finding bugs is not about payloads.
It is about identifying **state, trust, and missing revalidation**.

---

## The Three Mandatory Questions (Non-Negotiable)

For **every endpoint**, always ask:

1. What **state** is created, modified, or relied upon?
2. What **value does the server trust**?
3. Is **ownership / integrity / intent revalidated every time** using the session?

If revalidation is missing → vulnerability exists.

---

## State
State is any server-side data that persists or influences behavior.

Examples:
- User profile data (email, password, role)
- Orders, invoices, tickets
- Session state
- Password reset flows
- Multi-step workflows

If state exists, it must be protected.

---

## Trust Points
A trust point is any value the server **assumes to be correct**.

Common dangerous trust sources:
- Object IDs (`id`, `userId`, `accountId`)
- UUIDs
- Hidden form fields
- JSON parameters
- Tokens reused across steps
- Client-side enforced logic

Rule:
> All request data is attacker-controlled.

---

## Revalidation (The Real Security Control)
Revalidation means the server **re-derives truth** on every request.

Correct trust source:
- Session → server-side user identity
- Database ownership checks
- Role checks enforced per action
- Flow/state verification on every step

If the server trusts client-supplied values instead → broken access control or logic flaw.

---

## Canonical Example: Email Update (IDOR)

### State
- User email stored in database

### Trusted Value (Bug)
- `user_id` / `id` parameter from client

### Failure
- Server assumes the ID belongs to the authenticated user

### Result
- Horizontal privilege escalation (IDOR)

### Root Cause
The server fails to derive ownership from the session on every request.

---

## Generalization Across Vulnerability Classes

| Vulnerability | State Affected        | Trusted Value        | Missing Revalidation |
|---------------|-----------------------|----------------------|----------------------|
| IDOR          | User/Object data      | Object ID            | Ownership            |
| CSRF          | Account action        | Session cookie       | User intent          |
| Auth Logic    | Login / reset flow    | Token / step         | Flow integrity       |
| SQL Injection | Query execution       | User input           | Query structure      |
| XSS           | DOM / page content    | Untrusted data       | Output context       |

Different bugs.  
Same root cause: **broken trust assumptions**.

---

## Backend Flow (Where Bugs Actually Happen)

1. **HTTP Request**
   - Everything is attacker-controlled (params, body, headers, cookies)

2. **Routing**
   - Request mapped to backend logic

3. **Backend Logic**
   - Data is processed or stored
   - No bug unless data reaches a sink unsafely

4. **Sink**
   - Sensitive operation where data is interpreted or executed

Common sinks:
- HTML rendering → XSS
- SQL queries → SQLi
- File operations
- Privileged actions

5. **Context**
   - Determines how the sink interprets data

Same input, different context → different risk.

---

## XSS as a Trust Failure (Backend View)

### Core Idea
XSS occurs because the backend **trusts data to be safe for a context**.

- Execution happens at **render time** in the browser
- Not at input time
- Not at storage time

Rule:
> Validate early, encode late.

Encoding must happen:
- At output
- Based on the exact context (HTML, attribute, JS, etc.)

---

## Payloads (Important Perspective)
Payloads are **proof**, not the vulnerability.

- A vulnerability exists even if no payload works
- Payloads only demonstrate impact

Junior mindset:
> “Which payload worked?”

Senior mindset:
> “Why was this value trusted at this sink?”

---

## Chaining Trust Failures (Impact Thinking)

Real-world impact usually comes from **multiple broken trust assumptions**.

### Chain Model

Trust Failure #1  
↓  
New capability gained  
↓  
Trust Failure #2  
↓  
Higher-impact state change

---

### Example Chain: Stored XSS → IDOR → Account Takeover

**Step 1 – Stored XSS**
- State: Stored user content
- Trusted value: User input
- Gain: JavaScript execution in victim’s browser

**Step 2 – IDOR**
- State: User profile data
- Trusted value: Object ID
- Gain: Modify victim’s data using their session

**Step 3 – Auth Abuse**
- State: Email / password reset
- Trusted value: Session or email ownership
- Result: Account takeover

Each step compounds trust failures.

---

## CSRF vs XSS (Trust Difference)

- CSRF breaks **user intent**
- CSRF cannot read responses
- XSS breaks **everything**
- XSS removes the need for CSRF entirely

---

## Final Rule (Personal Non-Negotiable)
No exploit attempt without explicitly identifying:
- State
- Trust point
- Revalidation logic

Payloads come last.

---

## One-Line Summary
Backend vulnerabilities exist because the server trusts client-controlled data or fails to revalidate trust on every request.
