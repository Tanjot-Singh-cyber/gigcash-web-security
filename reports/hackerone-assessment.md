# Security Assessment Report — hackerone.com

**Prepared by:** Tanjot Singh  
**Date:** March 2026  
**Testing Period:** Days 46–48  
**Tools:** Burp Suite, Chrome Browser  
**Scope:** hackerone.com, HAI feature, /edit-profile  

---

## 1. Executive Summary

I performed a manual security check on hackerone.com to look for common web vulnerabilities. My testing focused on how the site handles user input and how the API communicates with the server. After three testing sessions, no security vulnerabilities were found. The website has very strong protections in place that prevent common attacks from working.

---

## 2. Methodology

I used a black-box testing approach. This means I tested the site from the perspective of an outside user. I used Burp Suite to monitor the data being sent between my browser and the server. I specifically looked for ways to bypass security filters or access data that shouldn't be public.

---

## 3. Areas Tested

### A. GraphQL API — `/graphql`

I checked the `/graphql` endpoint to see if I could extract sensitive information about the database structure.

- **Introspection:** I tried to pull the full API schema, but the server blocked the request. This is a good security measure.
- **User Data:** I could only see public profile information. However, I did find a hidden field for `address` (street, city, etc.). While it only returned `null` (empty) results during my test, I recommend double-checking that this field is properly locked down so users can't see each other's private addresses.

**Conclusion: SAFE**

---

### B. HAI Report Assistant

I tested the search bars and feedback forms in the help section.

- **XSS Testing:** I tried to inject code into the search fields to see if it would execute in the browser. The server did not echo my input back, which successfully prevents Reflected Cross-Site Scripting (XSS).
- **Data Exposure:** I noticed that some report details (like bounty amounts) are visible to everyone, even without logging in. After reviewing the site's features, I determined this is intentional for transparency and not a bug.
- **Feedback Form:** The feedback form submits data to `api-iam.intercom.io` — a third party service. Out of scope.

**Conclusion: SAFE**

---

### C. Profile Settings — `/edit-profile`

I tested the Edit Profile page to see if I could save malicious code into my user bio or name.

- **Input Validation:** I tried to bypass the browser's filters to upload a `javascript:` link in the website field. The server recognized the attempt and blocked it.
- **Output Encoding:** I successfully saved a `<script>` tag in my bio, but the website automatically converted the code into plain text before displaying it. This prevents the code from ever running.
- **CSP:** Even if output encoding were bypassed, the Content Security Policy header blocks inline script execution entirely.

**Conclusion: SAFE**

---

## 4. Observations

- GraphQL introspection is disabled — good security practice
- Security headers are comprehensive: `X-Frame-Options`, `X-Content-Type-Options`, `Strict-Transport-Security`, `Content-Security-Policy`
- Server-side validation mirrors client-side validation — no bypass possible
- Hidden `address` field exists on the User type in GraphQL — returned null for all users tested, access control unconfirmed

---

## 5. Overall Conclusion

**Status: SAFE**

HackerOne uses a defense-in-depth strategy — multiple layers of protection working together. Output encoding and CSP act as backups to each other. Even when one layer is tested, another catches it.

**Recommendation:** The only minor observation is the hidden `address` field in the GraphQL API. It appears safe based on testing, but worth a secondary check to ensure it remains private across all user types.
