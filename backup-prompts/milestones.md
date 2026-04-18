# GIGCASH — Master Roadmap & Skill Tracker
> Last updated: April 2026  
> Goal: Become a backend-aware, AI-security-literate AppSec engineer by 2028  
> Target: 14+ LPA product security role via off-campus applications  
> Philosophy: Reasoning-first. Trust boundaries before payloads. Proof over claims.

---

## Context & Reality Check

The AppSec landscape is shifting. AI models (Claude Mythos, etc.) are beginning to automate raw vulnerability discovery. The valuable AppSec engineer of 2028 is not the one who finds the most bugs — it's the one who:

- Understands *why* a vulnerability matters in a business context
- Can threat model a product before it's built
- Can secure AI-powered applications
- Can communicate risk and drive remediation

This roadmap is built around that reality.

---

## Overall Progress

| Area | Status |
|---|---|
| Core AppSec Thinking | 🟡 ~70% |
| Web Vulnerability Coverage | 🟡 ~50% |
| Real Application Testing | 🟡 ~40% |
| AI / LLM Security | 🔴 0% |
| Threat Modeling | 🔴 0% |
| Backend Awareness | 🔴 ~15% |
| Secure Code Review | 🔴 0% |
| DSA (Minimum Viable) | 🟡 ~30% |
| Tools | 🟡 ~40% |
| Projects | 🔴 ~10% |
| Writing & Proof | 🟡 ~35% |
| Job Readiness | 🔴 ~20% |

---

## Phase Structure

```
Phase 1 (DONE)        — Core web vulns via PortSwigger labs
Phase 2 (NOW)         — Real-target testing + LLM security intro
Phase 3 (Jul–Dec 26)  — Threat modeling + DSA + Backend mini app
Phase 4 (Jan 27+)     — AI security depth + job prep + applications
```

---

## A. Core AppSec Thinking (FOUNDATION — NON-NEGOTIABLE)

- ✅ Identify current state before testing any feature
- ✅ Identify state transition (what changes?)
- ✅ Identify identity carrier (session, JWT, userId, token)
- ✅ Identify trust boundaries (client → server → backend → DB)
- 🟡 Label source → sink → context before payloads
- ✅ Stop testing if classification is unclear
- ✅ Explain why attacks fail, not only why they work
- ✅ Avoid payload spraying
- 🟡 Think in authorization logic, not inputs
- 🔴 Apply trust boundary thinking to AI/LLM systems
- 🔴 Threat model a feature before touching it

---

## B. Web Vulnerability Coverage

### Authentication & Identity
- ✅ Login flow analysis
- 🟡 Session vs JWT (conceptual, limited real exposure)
- 🟡 Cookie flags (HttpOnly, Secure, SameSite)
- 🟡 Logout & session invalidation
- ✅ Password reset flow mapping
- 🔴 Token generation, expiry, reuse risks
- 🔴 Account recovery abuse paths

### Access Control
- ✅ Vertical access control
- ✅ Horizontal access control (IDOR)
- ✅ Role vs permission confusion
- ✅ User-controlled identifiers
- ✅ Multi-step authorization bypass
- 🟡 Forced browsing
- 🟡 Business rule enforcement gaps

### Input Handling
- ✅ XSS (HTML, attribute, JS, URL contexts)
- ✅ Stored vs reflected vs DOM XSS
- 🟡 SQL injection (classic + blind)
- 🟡 Injection vs logic flaw distinction
- 🔴 Output encoding vs validation (design-level)

### State & Logic Bugs
- ✅ CSRF as state-change abuse
- 🟡 Replay attacks
- 🟡 Missing state validation
- 🟡 Inconsistent backend checks
- 🔴 Race conditions

### File Handling
- 🔴 File upload flow mapping
- 🔴 Content-type trust issues
- 🔴 File storage location risks
- 🔴 Access control on uploaded files
- 🔴 Download authorization checks

---

## C. AI / LLM Security ⚡ NEW PRIORITY

> Why: AI products are the fastest growing attack surface. Almost no fresher is building here. This is the timing advantage.

### OWASP Top 10 for LLMs
- 🔴 LLM01 — Prompt Injection
- 🔴 LLM02 — Insecure Output Handling
- 🔴 LLM03 — Training Data Poisoning
- 🔴 LLM04 — Model Denial of Service
- 🔴 LLM05 — Supply Chain Vulnerabilities
- 🔴 LLM06 — Sensitive Information Disclosure
- 🔴 LLM07 — Insecure Plugin Design
- 🔴 LLM08 — Excessive Agency
- 🔴 LLM09 — Overreliance
- 🔴 LLM10 — Model Theft

### Hands-on
- 🔴 Test prompt injection on a real LLM-powered app
- 🔴 Understand RAG poisoning conceptually
- 🔴 Understand model supply chain attack surface
- 🔴 Build + attack your own LLM feature (Project 3)

---

## D. Threat Modeling ⚡ NEW PRIORITY

> Why: This is judgment work. Can't be automated. Core AppSec engineer skill.

- 🔴 Understand STRIDE framework (Spoofing, Tampering, Repudiation, Info Disclosure, DoS, Elevation)
- 🔴 Apply STRIDE to one target before testing it
- 🔴 Draw a trust boundary diagram for a real app
- 🔴 Document threats before looking for vulns
- 🔴 Write one formal threat model document

---

## E. Secure Code Review ⚡ NEW PRIORITY

> Why: Black-box testing gets automated. Reading code for security issues is a core AppSec engineer job function.

- 🔴 Read a Flask/FastAPI app and identify auth checks
- 🔴 Find a missing authorization check in open source code
- 🔴 Understand what a secure vs insecure route looks like
- 🔴 Review a GitHub PR for security issues (any open source project)
- 🔴 Write a code review comment explaining a security risk

---

## F. Backend Awareness

Chosen stack: Python + Flask / FastAPI

- 🟡 Request lifecycle
- 🔴 Routes & controllers (hands-on)
- 🔴 Middleware & auth checks
- 🔴 Role checks in code
- 🔴 ORM basics (queries, filters)
- 🔴 Error handling patterns
- 🟡 Common developer mistakes (theoretical)

---

## G. Database & Auth Fundamentals

- 🟡 Basic SQL (SELECT, WHERE, JOIN, LIMIT)
- 🟡 User ID storage patterns
- 🔴 Foreign key relationships
- 🟡 Password hashing vs encryption
- 🔴 Token storage strategies
- 🔴 Common auth implementation flaws (code-level)

---

## H. DSA — Minimum Viable Only

> Target: ~150 LeetCode problems by internship applications (Oct 2026)  
> Current: ~53 solved

- 🟡 Arrays & strings
- 🟡 Two pointers
- 🟡 Hash maps
- 🟡 Binary search
- 🔴 Stacks & queues
- 🔴 Basic recursion
- 🟡 Time & space complexity intuition

🚫 No advanced DP  
🚫 No competitive programming grind  
🚫 DSA is never the primary focus — AppSec first

---

## I. Tools

- 🟡 Burp Suite (manual testing, fluency building)
- 🟡 Browser DevTools (network, storage)
- 🔴 Postman / API testing basics
- 🟡 Git (clone, commit, push, README)
- 🟡 Basic Linux commands
- 🔴 Basic static analysis (grep for secrets, unsafe functions)

---

## J. Projects

### Project 1 — Backend Mini App (Phase 3)
Build a simple web app with auth, roles, password reset, file upload. Then break your own app. Document every security issue and fix.
- 🔴 Auth system (login, session, logout)
- 🔴 Roles (user/admin)
- 🔴 Password reset flow
- 🔴 File upload with access control
- 🔴 Break own app
- 🔴 Document security issues & fixes

### Project 2 — Real App Security Assessment (Ongoing)
Formal assessment reports on real targets. Already started.
- ✅ hackerone.com assessment report
- ✅ tines.com assessment report
- 🟡 subspace.money (finding submitted)
- 🔴 2 more formal reports by end of Phase 2

### Project 3 — LLM Security Project ⚡ NEW (Phase 3/4)
Build a simple LLM-powered feature (chatbot, assistant, anything). Then attack it. Document prompt injection paths, output handling issues, excessive agency risks.
- 🔴 Build a basic LLM-integrated app
- 🔴 Attack it using OWASP LLM Top 10
- 🔴 Write a formal security assessment of your own app
- 🔴 Publish on GitHub

---

## K. Writing & Proof

- 🟡 Clear README structure
- 🟡 Structured assessment reports (2 done)
- 🔴 Diagrams > payload dumps
- 🟡 Explain thinking verbally
- 🟡 Daily/weekly progress tracking
- 🔴 Lessons learned notes per target
- 🔴 LinkedIn posts per finding or assessment

---

## L. Job Readiness

Target: Internship applications October–November 2026  
Target role: Product security / AppSec internship, 14+ LPA full-time by 2028

- 🔴 Resume focused on reasoning, not tool lists
- 🟡 Can explain one real bug deeply
- 🟡 Can explain one failure confidently
- 🟡 Honest about limits
- 🟡 GitHub portfolio public and structured
- 🔴 Can threat model a feature verbally in an interview
- 🔴 Can explain what AI security means and why it matters
- 🔴 Mock interview readiness

---

## Phase Timeline

### Phase 2 — Now through July 2026
**Primary:** Real-target testing (HackerOne, Bugbase)  
**Parallel (new):** OWASP LLM Top 10 — 2-3 hours/week  
**Parallel (new):** Start reading open source Flask code for security issues  
**Output:** 2 more formal assessment reports + LLM Top 10 complete

### Phase 3 — August–December 2026
**Primary:** DSA minimum viable (target 150 problems)  
**Primary:** STRIDE threat modeling — apply to every target  
**Primary:** Backend mini app — build + break  
**Output:** Project 1 complete, threat model documented, internship applications live

### Phase 4 — January 2027+
**Primary:** LLM security project (Project 3)  
**Primary:** Secure code review practice  
**Primary:** Interview prep  
**Output:** Full-time applications, 14+ LPA target

---

## Differentiator Statement (for interviews)

> "I understand web application security from the trust boundary up — not from a payload list down. I've tested real targets, submitted findings, and I'm building toward AI/LLM security because that's where the attack surface is moving. I can threat model a feature, review code for authorization gaps, and explain risk in business terms."

---

## Bug Bounty Log Summary

| Target | Platform | Finding | Status |
|---|---|---|---|
| hackerone.com | HackerOne | No findings — strong defenses documented | Closed |
| tines.com | HackerOne | No findings — mass assignment ruled out | Closed |
| subspace.money | Bugbase | IDOR / getBankDetails GraphQL — High severity | Submitted, awaiting triage |

---

## Rules (from RULES.md)

1. Reasoning before payloads — always
2. Classify the vulnerability before testing it
3. Document failures as thoroughly as findings
4. One target at a time, full coverage
5. No tool collecting — understand what you use
6. No side project escapes when current work gets hard
7. Proof over claims — GitHub or it didn't happen
