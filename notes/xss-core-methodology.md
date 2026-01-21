# XSS Core Methodology – How to Think, Not Guess

## The One Pattern Common to All XSS Types
Regardless of whether XSS is reflected, stored, or DOM-based,
the exploitation process always follows the same steps:

1. Send a harmless input
2. Observe where the input appears
3. Identify how it is transformed
4. Determine the final execution context
5. Craft a payload based on context, not guesswork

XSS is about escaping context, not injecting JavaScript.

---

## Identifying Where Input Comes From (Source)

### Request (Reflected XSS)
- Input appears immediately after submission
- Present in server response
- Visible in View Page Source

### Database (Stored XSS)
- Input persists after refresh
- Appears for other users
- Not tied to current request parameters

### Browser (DOM XSS)
- Input comes from URL, hash, or storage
- View Page Source does not show it
- Vulnerability exists only in JavaScript execution

---

## Identifying Input Handling (Constraints)

### Encoding
- < becomes &lt;
- > becomes &gt;
- Indicates HTML encoding

### Escaping
- ' becomes \'
- " becomes \"
- Indicates JavaScript string escaping

### Concatenation
- Input is joined using + operators
- Context depends on surrounding code

### Sanitization
- Certain tags or characters removed
- Filtering does not mean safety

### Client vs Server Handling
- Disable JavaScript → issue disappears = client-side
- URL-only manipulation works = DOM-based

---

## Identifying Where Input Lands (Context)

Common execution contexts:
- HTML body
- HTML attribute
- JavaScript string
- URL (href/src)
- DOM sink-created context

Payload choice is dictated by this context.

---

## Key Realization
Payloads are not creative.
They are a mechanical result of:
- source
- transformations
- context

If a payload fails, the analysis was wrong — not the syntax.
