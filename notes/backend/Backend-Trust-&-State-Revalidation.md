# Day 34 – Backend Trust & State Revalidation

## Goal
Understand why backend vulnerabilities occur by identifying state, trust points, and missing revalidation.

## Core Questions (Day 34 Rule)
- What state is created or changed?
- Which endpoints touch this state?
- What value does the server trust later?
- Is ownership re-validated every time using the session?

If ownership is not re-derived from the session on every request, a vulnerability exists.

## Lab
PortSwigger – User ID controlled by request parameter  
Category: Access Control / IDOR

## State
- User account data (email address)

## Endpoints
- View account
- Update email

## Trust Point (Vulnerability)
- Client-supplied user identifier (`userId` / `id`)

## Correct Trust Source
- Session cookie → server-side mapped user identity

## Vulnerability Type
- Horizontal Access Control (IDOR)

## Root Cause
The application allows horizontal privilege escalation because it trusts a client-supplied user identifier instead of deriving user identity from the session cookie.

## Key Learning
Authentication is not authorization.  
Login alone does not guarantee ownership checks.

## Reusable Pattern
This trust failure pattern applies to:
- IDOR
- Access Control
- CSRF
- Chained attacks using XSS
