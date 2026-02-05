# Chaining Trust Failures (Impact Thinking)

## Core Idea
Real-world impact usually comes from **chaining multiple trust failures**, not from a single bug.

Instead of asking:
- “Is this vulnerable?”

Ask:
- “What new capability does this vulnerability give me?”
- “What other feature now trusts that new position?”

---

## Chain Model

Trust Failure #1  
↓  
New capability gained  
↓  
Trust Failure #2  
↓  
Higher-impact state change

---

## Example Chain: Stored XSS → IDOR → Account Takeover

### Step 1 — Stored XSS (Comment / Hyperlink)

**State**
- Comment content rendered in victim’s browser.

**Trusted Value**
- User-generated content assumed to be safe.

**Failure**
- No proper output encoding.

**Gain**
- JavaScript execution in victim’s browser (browser control).

---

### Step 2 — IDOR via XSS (Horizontal Access Control)

**State**
- User profile data.

**Trusted Value**
- `user_id` / `id` parameter.

**Failure**
- Server does not revalidate object ownership.

**Gain**
- Ability to read or modify other users’ data using victim’s session.

---

### Step 3 — Authentication Abuse / Account Takeover

**State**
- Account credentials (email / password).

**Trusted Value**
- Email ownership or session authenticity.

**Failure**
- Email change or password reset trusted without strong re-authentication.

**Result**
- Account takeover.

---

## Where CSRF Fits

- CSRF causes **forced state-changing actions** using victim’s browser.
- CSRF does **not** allow reading sensitive data.
- XSS removes the need for CSRF by bypassing user-intent checks entirely.

---

## Key Takeaway
Each vulnerability should be evaluated by:
- What trust assumption breaks
- What new capability is unlocked
- Whether that capability can be chained into higher impact

Finding bugs is junior work.  
**Chaining trust failures is senior AppSec thinking.**
