# Gigcash – Web Application Security Research
**Tanjot Singh** | B.Tech CSE (Cybersecurity), MIET Jammu  
[LinkedIn](https://www.linkedin.com/in/tanjot-singh-69579a352)

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
| blombooru (FastAPI) | SSRF — unvalidated URL parameter in `/proxy-image`, bypasses `startswith("http")` check — CWE-918 | High | GHSA-5c5w-x8jp-fjqw · Fix planned v1.40.0 · CVE in progress |
| newspipe (Flask) | SSRF bypass — `validate_url()` called on feed CREATE path but absent on feed EDIT path — CWE-918 | Medium | Disclosed to maintainer via email |
| mediaflow-proxy (FastAPI) | Unauthenticated SSRF — `api_password` defaults to `None`, no destination validation — CWE-918 | Medium | Disclosed to maintainer via email |
| subspace.money | IDOR/BOLA — `getBankDetails` GraphQL query accepts cross-user UUIDs without authorization check — CWE-639 | High | Submitted to Bugbase |

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
- SSRF — server-side request forgery, internal network enumeration, cloud metadata access
- Access Control — multi-step workflows, identity vs session confusion
- Business Logic — trust chaining, state manipulation
- Command Injection — shell=True sinks, argument injection
- SSTI — Jinja2 template injection
- XXE — external entity injection via XML parsers
- Insecure Deserialization — pickle.loads on attacker-controlled input

---

## Tools

Burp Suite, Browser DevTools, PortSwigger Web Security Academy, 
Semgrep, Bugbase, GitHub Security Advisories
