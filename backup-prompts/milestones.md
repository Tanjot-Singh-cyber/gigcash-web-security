# Gigcash — Learning Goals (Current Status Snapshot)

> AppSec-first learning path focused on real applications, state & trust analysis, and proof-based growth.  
> Goal: Graduate as a backend-aware, hireable AppSec fresher.

---

## 🧠 A. Core AppSec Thinking (FOUNDATION — NON-NEGOTIABLE)

- ✅ Identify current state before testing any feature  
- ✅ Identify state transition (what changes?)  
- ✅ Identify identity carrier (session, JWT, userId, token)  
- ✅ Identify trust boundaries (client → server → backend → DB)  
- 🟡 Label source → sink → context before payloads (improving consistency)  
- ✅ Stop testing if classification is unclear  
- ✅ Explain why attacks fail, not only why they work  
- ✅ Avoid payload spraying  
- 🟡 Think in authorization logic, not inputs (strong, still maturing)

---

## 🔐 B. Web Vulnerability Coverage (DEPTH > BREADTH)

### Authentication & Identity
- ✅ Login flow analysis  
- 🟡 Session vs JWT (conceptual clarity, limited real exposure)  
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
- 🟡 SQL injection (classic + blind, needs more real patterns)  
- 🟡 Injection vs logic flaw distinction  
- 🔴 Output encoding vs validation (design-level clarity pending)  

### State & Logic Bugs
- ✅ CSRF as state-change abuse  
- 🟡 Replay attacks  
- 🟡 Missing state validation  
- 🟡 Inconsistent backend checks  
- 🔴 Race conditions (basic understanding only)  

### File Handling
- 🔴 File upload flow mapping  
- 🔴 Content-type trust issues  
- 🔴 File storage location risks  
- 🔴 Access control on uploaded files  
- 🔴 Download authorization checks  

---

## 🌐 C. Real Application Analysis (KEY SEPARATOR)

- 🟡 Analyze 3+ real web apps (1 done, more needed)  
- 🟡 Map full app states (done once, needs repetition)  
- 🟡 Document trust assumptions  
- 🟡 Capture & diff real HTTP requests  
- 🟡 Identify where checks should exist  
- 🟡 Document failed exploit paths  
- 🔴 Write “why this is safe” notes (explicitly)  
- 🟡 Avoid scanner-only conclusions  

---

## 🧱 D. Backend Awareness (ONE STACK ONLY)

Chosen (conceptually):  
- 🟡 Python + Flask / FastAPI (not yet hands-on)

### Must understand:
- 🟡 Request lifecycle  
- 🔴 Controllers & routes (hands-on pending)  
- 🔴 Middleware & auth checks  
- 🔴 Role checks in code  
- 🔴 ORM basics (queries, filters)  
- 🔴 Error handling patterns  
- 🟡 Common developer mistakes (theoretical)

---

## 🗄️ E. Database & Auth Fundamentals

- 🟡 Basic SQL (SELECT, WHERE, JOIN, LIMIT)  
- 🟡 User ID storage patterns  
- 🔴 Foreign key relationships  
- 🟡 Password hashing vs encryption  
- 🔴 Token storage strategies  
- 🔴 Common auth implementation flaws (code-level)

---

## 📐 F. System Design (SECURITY-AWARE)

- 🟡 Basic web architecture  
- 🟡 Authentication flow design  
- 🟡 Password reset design  
- 🔴 File upload & download design  
- 🔴 API design with auth checks  
- 🟡 Trust boundaries in diagrams  
- 🟡 Design-level security risks  
- 🔴 Security vs usability trade-offs  

---

## 🧮 G. DSA — MINIMUM VIABLE

- 🟡 Arrays & strings  
- 🔴 Hash maps  
- 🔴 Stacks & queues  
- 🔴 Basic recursion  
- 🟡 Time & space complexity intuition  
- 🟡 Ability to read code calmly  

🚫 No advanced DP  
🚫 No competitive programming grind  

---

## 🛠️ H. Tools (UNDERSTAND, DON’T COLLECT)

- 🟡 Burp Suite (manual testing, fluency building)  
- 🟡 Browser DevTools (network, storage)  
- 🔴 Postman / API testing basics  
- 🟡 Git (clone, commit, push, README)  
- 🟡 Basic Linux commands  

---

## 🧪 I. Projects (QUALITY > QUANTITY)

### Project 1 — Backend Mini App
- 🔴 Auth system  
- 🔴 Roles (user/admin)  
- 🔴 Password reset  
- 🔴 File upload  
- 🔴 Break own app  
- 🔴 Document security issues & fixes  

### Project 2 — Security Analysis Project
- 🟡 Real app analysis  
- 🔴 State diagrams  
- 🔴 Trust mapping (formal)  
- 🔴 Abuse scenarios  
- 🔴 No exploit required (documented)  

### Project 3 (Optional)
- 🔴 Secure redesign of weak flow  
- 🔴 Explain trade-offs  

---

## ✍️ J. Writing & Proof (MOST IMPORTANT)

- 🟡 Clear README structure  
- 🔴 Short, structured writeups  
- 🔴 Diagrams > payload dumps  
- 🟡 Explain thinking verbally (written pending)  
- 🟡 Track daily / weekly progress  
- 🔴 Maintain “lessons learned” notes  

---

## 🎓 K. Internship & Job Readiness

- 🔴 Resume focused on reasoning  
- 🟡 Explain one bug deeply (verbally)  
- 🟡 Explain one failure confidently  
- 🟡 Honest about limits  
- 🔴 Show GitHub proof (public)  
- 🟡 Practice verbal flow explanations  

---

## 🎯 End Goal

Status: 🟡 **IN PROGRESS**

Target:
- Calm, backend-aware AppSec fresher  
- Strong in state & trust reasoning  
- Comfortable with real apps  
- Trainable and hireable  

Current overall completion: **~40–45%**
