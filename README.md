# Gigcash – Web Application Security Research

**Tanjot Singh** | B.Tech CSE (Cybersecurity), MIET Jammu  
[LinkedIn](https://www.linkedin.com/in/tanjot-singh-69579a352) • 
[HeaderGuard](https://github.com/Tanjot-Singh-cyber/headerguard)

---

## What This Is

A structured, self-directed AppSec research program built around 
real findings, OSS vulnerability research, and production-grade 
security tooling.

Every target is approached the same way:

1. Map the state — what is the application tracking?
2. Identify trust boundaries — what does the server blindly trust?
3. Form a hypothesis — what could break if that trust is wrong?
4. Test to confirm or disprove

---

## Confirmed Security Findings

| Target | Vulnerability | Severity | Status |
|--------|--------------|----------|--------|
| blombooru (FastAPI) | SSRF — CWE-918 | High | [GHSA-5c5w-x8jp-fjqw](https://github.com/advisories/GHSA-5c5w-x8jp-fjqw) • CVE pending |
| newspipe | SSRF bypass — missing validate_url() on feed edit path | Medium | Disclosed to maintainer |
| subspace.money | IDOR/BOLA — getBankDetails GraphQL accepts cross-user UUIDs | High | Submitted to Bugbase |

---

## Projects

### HeaderGuard
HTTP security header analyzer + TLS/SSL inspector with AI-powered 
contextual analysis.

- Layer 7: HTTP security header analysis with weighted grading
- Layer 4: TLS/SSL inspection via pure Python stdlib
- AI layer: Gemini contextual analysis with confidence scoring
- Design principle: never outputs "Safe" — confidence scoring on every finding

[→ View HeaderGuard](https://github.com/Tanjot-Singh-cyber/headerguard)

---

## Structure

| Folder | Contents |
|--------|----------|
| `labs/` | PortSwigger labs — XSS, SQLi, CSRF, Access Control, Business Logic |
| `notes/` | Concept reasoning notes per vulnerability class |
| `reports/` | Security finding reports |

---

## Topics Covered

- XSS — DOM, Reflected, Stored (HTML, attribute, JS, URL contexts)
- SQL Injection — classic and blind
- CSRF — SameSite, SOP, token bypass
- IDOR / BOLA — horizontal and vertical privilege escalation
- SSRF — server-side request forgery, OOB detection via Burp Collaborator
- Access Control — multi-step workflows, identity vs session confusion
- Business Logic — trust chaining, state manipulation

---

## Tools

Burp Suite, Browser DevTools, PortSwigger Web Security Academy, 
Semgrep, Bugbase
