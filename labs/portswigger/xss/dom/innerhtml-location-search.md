# DOM XSS via innerHTML (location.search)

## Classification
- Source: `location.search`
- Handler: JavaScript
- Sink: `innerHTML`
- Context: HTML body
- Type: DOM XSS

## Data Flow
User input is extracted from the URL query string and directly assigned to an element’s `innerHTML`, causing the browser to parse it as HTML.

## Why This Is Vulnerable
`innerHTML` treats user input as executable HTML. Without sanitization, any injected HTML that auto-executes will run in the browser.

## Why Backend Defenses Don’t Matter
The data is never processed by the backend. Since the vulnerability exists entirely in client-side JavaScript, server-side protections are irrelevant.

## Real-World Impact
This can be exploited to run arbitrary JavaScript in users’ browsers, enabling credential theft, malicious redirects, or persistent client-side attacks.
