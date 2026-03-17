# Gigcash – Web Application Security Research

**Tanjot Singh** | B.Tech CSE, MIET Jammu  
[LinkedIn](https://www.linkedin.com/in/tanjot-singh-69579a352)

---

## What This Is
A structured, self-directed AppSec research program built to develop 
real security skills through lab work, methodology, and real application analysis.

Every target is approached the same way:
1. Map the state — what is the application tracking?
2. Identify trust boundaries — what does the server blindly trust?
3. Form a hypothesis — what could break if that trust is wrong?
4. Test to confirm or disprove

---

## Current Phase
**Phase 1 Complete** — Lab foundations across all major web vulnerability classes  
**Phase 2 Active** — Real application analysis and HackerOne bug bounty testing

---

## Structure
| Folder | Contents |
|--------|----------|
| `labs/portswigger/` | Completed labs — XSS, SQLi, CSRF, Access Control, Business Logic |
| `notes/` | Concept reasoning notes per vulnerability class |
| `findings/` | Self-written security findings in report format |
| `real-app-analysis/` | Real application audits with trust boundary mapping |

---

## Topics Covered
- XSS — DOM, Reflected, Stored (HTML body, attribute, JS string, URL contexts)
- SQL Injection — including blind variants
- CSRF — SameSite, SOP distinctions, token bypass
- IDOR — horizontal and vertical privilege escalation
- Access Control — multi-step workflows, identity vs session confusion
- Business Logic — trust chaining, state manipulation
- Password Reset Poisoning — backend trust boundary analysis

---

## Tools
Burp Suite, Browser DevTools, PortSwigger Web Security Academy, HackerOne
