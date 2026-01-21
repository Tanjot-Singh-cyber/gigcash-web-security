# Stored XSS via Anchor href Attribute

Lab:
Stored XSS into anchor href attribute with double quotes HTML-encoded

Vulnerable Context:
<a id="author" href="USER_INPUT">Author name</a>
Context: HTML attribute (href)

Data Flow:
Website field -> href attribute
Name field -> anchor text
Comment field -> page content

Exploitation Strategy:
Inject a javascript: URL into the href attribute.

Final Payload:
javascript:alert(1)

Execution:
User clicks the author name link.
Browser executes the JavaScript.
Stored XSS fires.

Key Lesson:
When input is rendered inside an anchor href attribute,
javascript: URLs can directly lead to XSS.
