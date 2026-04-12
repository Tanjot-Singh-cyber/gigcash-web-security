# Security Research & Walkthroughs

This folder contains my deep-dives into real-world applications. These aren't just "bug hunts" — they are detailed records of how I mapped out attack surfaces, tested logic, and verified the security of major platforms during my bug bounty journey.

---

##  The Reports

### [hackerone.com](./hackerone-assessment.md) — March 2026
**Focus:** Breaking down GraphQL queries and testing how the site protects user data.

### [tines.com](./tines-assessment.md) — March 2026
**Focus:** Challenging AI assistants (Prompt Injection) and checking how the app handles private IDs.

---

##  The Goal

While no exploitable bugs were found in these specific targets, these files document my actual workflow:

- **Manual Testing:** Using Burp Suite to intercept and analyze hidden web traffic
- **Logic Checks:** Trying to bypass permissions or find mass assignment flaws
- **Defense Analysis:** Identifying why a site is secure — strong CSP, server-side validation, proper authorization

I write these reports to keep my testing organized and to show the behind-the-scenes of my security research process.
