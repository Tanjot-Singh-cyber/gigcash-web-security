# DOM XSS via document.write (location.search)

## Classification
- Source: `location.search`
- Handler: JavaScript
- Sink: `document.write`
- Context: HTML attribute (`img src`)
- Type: DOM XSS

## Data Flow
User-controlled input is read from the URL query string using JavaScript and directly written into the DOM using `document.write`, which parses the input as raw HTML.

## Why This Is Vulnerable
`document.write` inserts untrusted data into an HTML attribute context without sanitization or encoding, allowing HTML parsing and script execution in the browser.

## Why Backend Defenses Don’t Matter
The input never reaches the server. All processing happens client-side, so server-side validation, encoding, or WAFs cannot prevent this issue.

## Real-World Impact
An attacker could execute arbitrary JavaScript in a victim’s browser by crafting a malicious link, leading to session theft or account takeover.
