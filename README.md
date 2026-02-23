# gigcash-web-security

# Gigcash – Web Application Security Journey (AppSec)

This repository documents my structured journey into **Web Application Security (AppSec)** with a focus on **backend trust, state, and real-world vulnerability reasoning**.

This is not a collection of random labs or payloads.  
It is a **proof-based learning log** showing how I analyze applications the way real AppSec engineers do.

---

## Goal

- Build strong fundamentals in **Web Application Security**
- Develop a **state-first, trust-tracing mindset**
- Create **public proof** of AppSec skills for internships, gigs, and placements
- Transition from labs → real applications → professional AppSec work

---

## Core Methodology (Very Important)

For every feature or request, I follow the same workflow:

1. **What state is created or changed?**
2. **What value does the server trust later?**
3. **Where does that value come from?**
4. **Is ownership / identity revalidated every time?**

If trust crosses a boundary without revalidation → **vulnerability exists**.

This methodology applies across:
- Access Control
- IDOR
- CSRF
- Authentication
- Password reset flows
- Business logic flaws

---

## What I’ve Covered So Far (Phase 1 – ~70% Complete)

### Vulnerability Classes
- Cross-Site Scripting (XSS) – all contexts
- SQL Injection (including blind SQLi)
- CSRF (state & trust based)
- Access Control
  - Horizontal privilege escalation
  - Vertical privilege escalation
  - IDOR
- Backend trust boundaries
- Multi-step trust failures
- Password reset poisoning
- Identity vs session confusion
- Client-controlled userId issues

### Skills Developed
- Reading and reasoning about HTTP requests
- Tracing backend trust decisions
- Identifying identity and authorization flaws
- Breaking complex flows into analyzable states
- Explaining vulnerabilities clearly and correctly

---

- **labs/** → Practical lab work (PortSwigger-based)
- **notes/** → One-page reasoning notes (state, trust, impact)
- **progress.md** → Day-by-day learning log

---

## Tools Used
- Burp Suite (Proxy, Repeater)
- Browser DevTools
- PortSwigger Web Security Academy

No automation-heavy tools yet — focus is on **understanding before tooling**.

---

##  What’s Next (Phase 1 Remaining)
- Authentication edge cases (session fixation, remember-me, login CSRF)
- Business logic vulnerabilities
- File upload & processing trust
- API security fundamentals
- Revision + articulation (writing and explaining findings)

After Phase 1:
 **Phase 2: Proof + Projects + Light Scripting**

---

## Why This Repo Exists

Most beginners learn *what payload to send*.  
This repo shows **why the backend breaks**.

If you’re reviewing this repo, you’re not seeing memorized vulnerabilities —  
you’re seeing **how I think about applications**.

---

##  Author
**Tanjot Singh**  
AppSec-focused student | Web Security | Backend Trust Analysis

---

> “Vulnerabilities are not bugs in code.  
> They are bugs in trust.”

## Learning AppSec through backend trust & state analysis

