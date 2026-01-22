# Reflected XSS in JavaScript string (script tag context)

## Lab
Reflected XSS into a JavaScript string with single quote and backslash escaped

---

## Injection point
User-controlled input is reflected inside a JavaScript string within a `<script>` tag:

<script>
var searchTerms = 'USER_INPUT';
</script>

The script executes on page load.

---

## Initial observation
- Input appears inside a single-quoted JavaScript string
- Single quotes are escaped (`'` → `\'`)
- Backslashes are escaped
- Direct JavaScript string break-out is prevented

Payloads such as:
';alert(1)//
do not work due to proper JavaScript escaping.

---

## Key realization
The browser parses **HTML before JavaScript**.

Even when user input is placed inside a JavaScript string, the HTML parser will terminate a `<script>` block when it encounters:

</script>

This happens **before** JavaScript parsing and is not affected by JavaScript escaping.

---

## Exploitation
Because `<` and `>` are not HTML-encoded, the following payload is effective:

</script><script>alert(1)</script>

Effect:
- `</script>` closes the original script block
- A new `<script>` block is injected
- JavaScript executes immediately

---

## Why the defense failed
- JavaScript escaping was applied correctly
- HTML encoding was missing
- Escaping did not match the outermost parsing context (HTML)

---

## Root cause
User input is embedded directly inside a `<script>` tag without HTML encoding for `<` and `>`.

---

## Conclusion
JavaScript escaping alone is insufficient when user input is placed inside `<script>` tags.

HTML parsing rules override JavaScript string boundaries, enabling XSS via script-tag break-out.

---

## Status
Lab successfully exploited via HTML parser break-out.
