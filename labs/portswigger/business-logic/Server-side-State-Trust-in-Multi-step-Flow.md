# Lab - Exploiting a flawed business logic vulnerability

## Lab Level
Practitioner

## Goal
Understand how vulnerabilities arise when a server trusts previously created state without revalidation in later steps.

---

## Application Flow Observed
1. User adds product to cart
2. Server creates a session-bound cart state
3. User proceeds to checkout
4. Final transaction decision is made

---

## State Analysis

### What state is created?
A cart/transaction session is created during the add-to-cart or checkout initiation step.

### Where is the state stored?
Server-side (session-based storage).

### What does the server trust later?
The server trusts that:
- Product details
- Quantity
- Price  
were already validated when the cart was created.

### What check is skipped?
The server does **not** re-derive or revalidate:
- Product price
- Product quantity  
from a trusted server-side source during the final transaction step.

### What decision is made based on this trust?
The server completes the purchase using the previously stored cart state without revalidation.

---

## Key Insight
The vulnerability (or security decision) occurs **earlier** in the flow.  
Later requests appear clean because the critical trust decision has already been made.

---

## Learning Outcome
- Not all vulnerabilities are visible in the final request
- Clean-looking requests can still rely on flawed earlier trust
- Knowing when a flow is complete is as important as exploitation

---

## Verdict
This lab reinforces **state-first analysis** over payload-first testing and builds intuition for real-world applications where parameters are not always exposed.
