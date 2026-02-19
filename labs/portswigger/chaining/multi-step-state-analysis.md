#  Multi-Step Business Logic / State Corruption

## Summary
Identified a multi-step business logic vulnerability where client-controlled pricing corrupted server-side cart state, which was later trusted during checkout, enabling a purchase despite insufficient funds.

---

## Application Flow
1. User adds product to cart
2. Cart state (product, quantity, price) is stored server-side
3. User initiates checkout
4. Checkout endpoint relies entirely on stored cart state

---

## State Analysis

**Initial State**
- Authenticated user
- Cart exists server-side
- Account balance insufficient for legitimate purchase

**State Change**
- Cart updated via `/cart` endpoint
- Checkout performed via `/cart/checkout`

---

## Trust Boundary Breakdown

**Trusted Input (Incorrect)**
- `price` parameter supplied by client during cart update

**Server Assumption**
- Cart state is valid and untampered
- Checkout endpoint assumes all validations were enforced earlier

---

## Vulnerable Chain
POST /cart
→ client controls price
→ corrupted cart state stored

POST /cart/checkout
→ server trusts stored cart state
→ purchase allowed


The vulnerability is not present in a single request but emerges due to improper trust across workflow steps.

---

## Root Cause
Failure to re-validate critical business values (price) at the point of impact.  
Derived server-side state was treated as trusted despite originating from client input.

---

## Impact
- Business rule violation
- Product purchased with insufficient funds
- Financial loss scenario

---

## Correct Behavior
- Server must calculate price server-side
- All eligibility checks must be enforced atomically at checkout
- Client-supplied business values should never be trusted

---

## Key Learning
Server-side state is not inherently trusted.  
Any state derived from client input must be treated as untrusted until re-validated at the final action.


