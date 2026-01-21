# Reflected XSS – HTML Context (Nothing Encoded)

Lab:
Reflected XSS into HTML context with nothing encoded

Vulnerable Context:
User input is reflected directly into the HTML response.

Example:
<div>USER_INPUT</div>
Context: HTML body

Data Flow:
Query parameter -> HTML response

Why This Is Vulnerable:
No encoding or sanitization is applied.
HTML tags are interpreted by the browser.

Exploitation Strategy:
Inject executable HTML or JavaScript directly into the page.

Example Payload:
<script>alert(1)</script>

Execution:
Payload is reflected in the response.
Browser parses the script tag.
JavaScript executes immediately.

Key Lesson:
When user input is reflected into HTML with no encoding,
direct script injection leads to instant XSS.

