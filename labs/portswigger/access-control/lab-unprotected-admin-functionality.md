# Lab: Unprotected Admin Functionality

## Goal
Access an admin-only function as a normal user and perform a privileged action.

---

## Steps
1. Logged in as a normal user
2. Discovered admin path via `/robots.txt`
3. Identified `/administrator-panel`
4. Accessed the endpoint directly via the browser
5. Server allowed access without authorization checks
6. Deleted user `carlos`

---

## Result
Lab solved successfully.

---

## Root Cause
The server failed to enforce role-based access control on the admin endpoint.

---

## Impact
Any authenticated (or unauthenticated) user could perform admin actions by directly accessing the endpoint.
