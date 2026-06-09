#  Phase 1

Day 1 – XSS basics (what XSS is, why it exists) – DONE

Day 2 – Reflected XSS (basic payloads, visible reflection) – DONE

Day 3 – Backend flow (request → processing → response mental model) – DONE

Day 4 – Stored XSS (data persistence, delayed execution) – DONE

Day 5 – Reflected vs Stored XSS (comparison, impact, use-cases) – DONE

Day 6 – Blind / Silent SQL Injection (behavior-based reasoning, no errors) – DONE

Day 7 – SQL Injection flow (source → query → database → response) – DONE

Day 8 – Hidden data & logic flaws (non-visible backend trust issues) – DONE

Day 9 – XSS backend explanation (how browsers execute injected JS) – DONE

Day 10 – DOM XSS fundamentals (client-side flow, JS sources & sinks) – DONE

Day 11 – DOM XSS using document.write (unsafe sinks) – DONE

Day 12 – DOM XSS using innerHTML (HTML parsing & execution) – DONE

Day 13 – DOM XSS via location.search (URL as attacker-controlled source) – DONE

Day 14 – Context awareness (HTML, attribute, JS, URL contexts) – DONE

Day 15 – DOM XSS lab series consolidation (multiple sinks, same source) – DONE

Day 16 – Attribute-context XSS introduction (breaking out of attributes) – DONE

Day 17 – Reflected XSS in JavaScript string (script tag context) - DONE

Day 18 – XSS consolidation & decision to park advanced WAF/CSP labs – DONE

Day 19 – CSRF fundamentals (authentication vs intent, request flow) – DONE

Day 20 – CSRF defenses (thinking-only: tokens, SameSite, Origin/Referer, POST myth) – DONE

Day 21 – CSRF vulnerability with no defenses (request analysis + exploit reasoning) – DONE

Day 22 – CSRF defenses & failure cases (tokens, SameSite, Origin/Referer, POST myth) – DONE

Day 23 – CSRF (request-method-dependent validation) – ANALYZED, PARKED

Day 24 – Access control fundamentals (authentication vs authorization, Subject/Object/Action, IDOR mental model) – DONE

Day 25 – Access control fundamentals (auth vs authz, subject-object-action model) – DONE

Day 26 – IDOR (horizontal access control) via predictable object IDs – DONE

Day 27 – Vertical Access Control: Unprotected admin functionality (forced browsing, robots.txt disclosure) – DONE

Day 28 – Horizontal Access Control (IDOR): identifying object references, modifying IDs to access other users’ data – DONE

Day 29 – IDOR: Horizontal privilege escalation via user-controlled identifier – DONE

Day 30 – IDOR with unpredictable UUIDs: leaking object references via blog content leading to API key disclosure – DONE

Day 31 – Function-Level Access Control (Vertical Privilege Escalation via client-controlled cookie) – DONE

Day 32 – Backend trust model: state identification, trust points, and revalidation logic across IDOR, CSRF, XSS, SQLi – DONE

Day 33 – Chaining trust failures: XSS → IDOR → authentication abuse, impact-driven vulnerability reasoning – DONE

Day 34 – Backend trust boundaries & horizontal access control (IDOR) – DONE

Day 35 – Multi-step state trust & password reset poisoning (trusting forwarded headers) – DONE

Day 36 – Vertical Access Control via Trust Chain Failure – DONE

Day 37 – Identity confusion via client-controlled user context (userId) – DONE

Day 38: Real application state & trust boundary analysis (password change flow) – DONE

Day 39: Validated password-change flow on a real app; confirmed server-side multi-step state enforcement prevents replay or out-of-context access  – DONE

Day 40 – Authorization Testing (Read-only API)  – DONE

Day 41 — Analyzed LMS state-adjacent endpoint (/lms/update-content-log) for sequence, replay, and auth misuse; confirmed proper session enforcement and no state-chaining vulnerability. – DONE

Day 42 — Exploited a multi-step business logic flaw by corrupting cart state via client-controlled pricing, which was later trusted at checkout, enabling purchase with insufficient funds.  – DONE

Day 43 — Isolated the authoritative patient update request in a SPA and analyzed session trust and object-level authorization for high-impact state changes.  – DONE

Day 44: Completed PortSwigger business logic lab by tracing server-side cart state, identifying the trusted decision point, and confirming no exploitable validation gap in the checkout flow. – DONE 

Day 45: Confirmed that after login the server must derive identity from the session (no userId in requests is correct) and that authorization failures mainly arise from missing object-ownership revalidation across endpoints. – DONE 

---------

 # Phase 2 — Real App Audit Mode

 Focus: Real targets on HackerOne. Feature auditing, 
authorization testing, state-trust reasoning. 
Producing SAFE/WEAK/VULN conclusions with written justification.

 Target platforms: HackerOne bug bounty programs
Primary tool: Burp Suite + Chrome

Day 46 — hackerone.com — GraphQL recon + User object enumeration — SAFE (unconfirmed) — Introspection disabled, 
address field exists on User type but returns null across all tested accounts, 
second test account needed to confirm access control

Day 47 — hackerone.com — HAI Report Assistant
1. Search input — keywords not reflected in response, XSS ruled out
2. Unauthenticated users can see metadata of undisclosed reports 
   (reporter, program, bounty, time) — concluded intentional by design
3. Feedback form submits to Intercom (api-iam.intercom.io) — 
   third party, out of scope

Day 48 — hackerone.com — /edit-profile stored XSS testing
1. Website field — server-side URL validation blocks 
   javascript: and data: URIs
2. Bio field — input stored but HTML encoded on output, 
   CSP blocks execution  
3. Name field — same, HTML encoded
Conclusion: No stored XSS. Defenses are application-wide 
— output encoding + CSP. Moving to api.hackerone.com next session.

Day 49 — tines.com — Profile update GraphQL mutation
contains hidden fields isAdmin and isActive not visible 
in UI but client-controlled. Potential mass assignment / 
privilege escalation. Needs second account to confirm.

Day 50 — tines.com — LLM chat, Story IDOR, Credit Allocation testing

1. LLM prompt injection — tested direct extraction, developer mode 
   jailbreak, context manipulation, and file upload injection. All 
   blocked. Well defended implementation. Not a finding.

2. Story name IDOR — decoded base64 story IDs (Story-112194 pattern), 
   tested sequential IDs via Burp Repeater. Server returns generic 
   "Story not found" for all non-owned IDs. Proper authorization 
   controls. Not a finding.

3. Credit allocation mutation — found teamAllocationChangeMutation 
   with base64 encoded teamIds. Tested negative values (rejected 
   server-side), large values (accepted — no upper bound). Attempted 
   cross-team modification of Team-104470 — appeared successful but 
   confirmed team belongs to own tenant. Not a cross-tenant IDOR.


Day 51 — tines.com — Target wrap-up
isAdmin/isActive fields — concluded safe. Server-side 
authorization likely validates role from session token, 
not client-supplied fields. Common pattern in mature GraphQL 
implementations. Not a finding.

Day 52 : Target: subspace.money (Bugbase)
Tested `subspace.money` (BugBase) and identified a Potential IDOR in the GraphQL `getBankDetails` query.
The endpoint fails to validate if the requesting user owns the targeted `user_id`, returning a `200 OK` for cross-user UUID queries. 
This creates an attack chain where financial PII (Bank/UPI info) could be leaked to any authenticated user.
Reported the lack of server-side access control as a High severity finding.

Day 53 : Target: `subspace.money` (BugBase)
 Activity: Conducted deep-dive authorization testing on GraphQL mutations and search filters.
 Findings: Verified that all write operations—specifically `whatsubCreateUPIIntentRequest`, `insert_whatsub_addresses_one`, and
 `whatsubAddAmountToCart`—properly enforce server-side access controls.
Conclusion: The platform shows a strong security posture across core logic,
 which highlights the getBankDetails query as a significant outlier and
 the only endpoint failing to validate cross-user UUID requests.ch highlights the getBankDetails query as a significant outlier and
 the only endpoint failing to validate cross-user UUID requests.


Day 54: Target: mrblomblo/blombooru (GitHub OSS)
Activity: Static code review — identified SSRF vulnerability.
Findings: The GET /api/booru-import/proxy-image endpoint accepts a user-supplied url parameter with no validation beyond startswith("http"). Traced user input through proxy_image() → get_client_for_url() → requests.get(). No IP validation, no blocklist, redirects enabled by default.
Submitted: GitHub Security Advisory GHSA-5c5w-x8jp-fjqw. Credit accepted. Pending developer response.
CWE: CWE-918 — Server-Side Request Forgery.

Day 55: Target: mrblomblo/blombooru (GitHub OSS)
Activity: Live PoC confirmation on local instance.
Setup: Installed PostgreSQL 18, configured .env, ran Blombooru locally.
PoC: Sent GET /api/booru-import/proxy-image?url=http://127.0.0.1:6379 — server returned proxy error confirming internal connection attempt. Sent http://127.0.0.1:8000 — server hung indefinitely in recursive self-request. Both confirm arbitrary internal request execution.
Updated advisory with live PoC evidence, screenshot, and Admin SSRF impact argument.
Severity: Medium standalone, High on cloud deployments.

Day 56: Target: edihasaj/tuspyserver (GitHub OSS)
Activity: Static code review + live testing — path traversal investigation.
Findings: uuid path parameter passed directly to os.path.join(files_dir, uid) without validation. Python confirmed path escapes files_dir via .. sequences. Upload-Concat header also passes bare UIDs without sanitization.
Live testing: Deployed under WSL using uvicorn and Hypercorn. Both servers normalize .. at HTTP layer per RFC 3986 — traversal blocked before reaching application code.
Conclusion: Code-level vulnerability, not exploitable via standard HTTP servers. Reported honestly via GitHub issue #83.
CVE: Not applicable — no confirmed exploitation path.
CWE: CWE-22 — Path Traversal (theoretical).

Day 57: 
Target: cedricbonhomme/newspipe
Vuln: SSRF protection bypass via feed edit endpoint
CWE: CWE-918 — Server-Side Request Forgery
Affected file: newspipe/web/views/feed.py — process_form()
Root cause: validate_url() called on feed CREATE but not on feed EDIT
Crawler fetches feed.link directly with no SSRF check
Reported: Email to cedric@cedricbonhomme.org
Status: Pending response

Day 58 : 
Target: CJackHwang/AIstudioProxyAPI (GitHub OSS, archived) 
Activity: Static code review — SSRF + auth bypass investigation. 
Findings: /api/proxy/test endpoint accepts user-controlled test_url parameter passed directly to httpx.AsyncClient.get() with follow_redirects=True and zero validation. 
APIKeyAuthMiddleware in app.py only protects /v1/ routes — /api/ routes including the proxy endpoint are completely unauthenticated. 
Compounding factor: default fresh install creates empty key.txt, causing verify_api_key() to return True for all requests. Full unauthenticated SSRF chain confirmed via static analysis. 
Live PoC not tested — Playwright + Camoufox setup required. CVE: Not assigned — repo archived, no disclosure channel available. 
CWE: CWE-918 — Server-Side Request Forgery. Status: Documented in findings/, no patch possible.


Day 59: Target: mhdzumair/mediaflow-proxy (GitHub OSS, 724 stars, active) Method: Static code review Findings:
api_password defaults to None — all proxy routes unauthenticated by default. 
sanitize_url() in routes/proxy.py fixes encoding only, no hostname/IP validation. 
Private ranges and loopback not blocked. Combined impact: unauthenticated SSRF via unvalidated destination parameter.
CWE: CWE-918 — Server-Side Request Forgery. Disclosed: Email to mhdzumair@gmail.com Status: Awaiting response


Day 60: Target: ParisNeo/lollms_hub Method: Semgrep + manual review 
19 findings — subprocess shell=True (hardcoded cmds, not exploitable), 
Flask | safe XSS (AI-generated content, not user input), bot tools RCE 
(internal AI tool, not HTTP exposed). All ruled out via manual analysis. 
Status: No exploitable findings.

Day 61: Target: scanapi/scanapi Method: Semgrep + manual review 
2 findings — eval() protected by RestrictedPython (by design), href XSS 
in local report template (no external users). All ruled out. 
Status: No exploitable findings.

Day 62: Targets: ReturnFI/Blitz, datawhores/OF-Scraper, deedy5/ddgs, zatosource/zato Method: Semgrep + manual review Blitz: Auth middleware applied globally, required API_TOKEN, subprocess hardcoded — no findings. OF-Scraper: SHA1 used intentionally to mimic OnlyFans signing algorithm (usedforsecurity=False) — not reportable. ddgs: CLI tool, user controls all inputs, no trust boundary — no findings. zato: 148 findings — XSS in admin panel (trusted users only), csrf_exempt on stub functions, legacy SSL/eval in patches — nothing exploitable. Status: No findings across all 4 targets.











