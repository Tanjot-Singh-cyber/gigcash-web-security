# Labs (Guided Practice)

This directory contains **guided security labs**, primarily from PortSwigger Web Security Academy.

## Scope
- Known vulnerability context (training environment)
- Goal is **learning methodology**, not discovery
- Focus on *how the vulnerability is identified and exploited*, not just the final payload

## What these labs demonstrate
- Request/response analysis
- Input → sink → context identification
- State and trust boundary reasoning
- Payload selection based on context (not trial-and-error)

## What these labs are NOT
- Not real-world vulnerability discovery
- Not bug bounty reports
- Not copy-paste solutions

## Structure
Labs are grouped by:
- Training source (e.g. `portswigger/`)
- Vulnerability class (XSS, SQLi, CSRF, Access Control, etc.)

Each lab writeup explains:
1. Initial application state
2. Trust assumptions made by the server
3. How user input flows to a sink
4. Why a specific technique works

Real application findings are **never** documented here.
See `/writeups` for independent, real-world work.
