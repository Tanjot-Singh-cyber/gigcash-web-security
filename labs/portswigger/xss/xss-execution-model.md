# XSS Execution Model (Mental Model Consolidation)

## Core Rule
XSS does NOT happen because user input exists.  
XSS happens ONLY when user-controlled input reaches a **JavaScript execution context** in the browser.

Input ≠ vulnerability  
Execution context = vulnerability

---

## Browser Model (Simplified)
The browser has two main interpreters:
- HTML parser
- JavaScript engine

XSS occurs when the HTML parser hands user input to the JavaScript engine.

---

## Executable JavaScript Contexts (High Risk)

User input is executable if it appears in:

1. `<script> ... </script>`
2. Event handler attributes  
   - `onclick`
   - `onfocus`
   - `onerror`
   - `onload`
3. `javascript:` URLs  
   - `<a href="javascript:alert(1)">`
4. JavaScript strings inside `<script>` blocks  
   - Breaking out of quotes enables execution
5. DOM sinks (client-side)
   - `innerHTML`
   - `document.write`
   - `outerHTML`

If input reaches any of the above without proper encoding → XSS is possible.

---

## Non‑Executable Contexts (Safe by Default)

User input is NOT executable when reflected as:

- Plain HTML text  
  - `<p>USER_INPUT</p>`
- Attribute values like:
  - `value="USER_INPUT"`
  - `<textarea>USER_INPUT</textarea>`
- URL parameters that remain part of a normal path or query string

These contexts do not invoke the JavaScript engine.

---

## Key Realizations

- We do not directly “see” server‑generated HTML.
- We infer execution using:
  - Browser behavior
  - DevTools (Elements, Network, DOM)
- No popup does NOT mean no vulnerability.
- Some XSS executes:
  - Later (Stored XSS)
  - After user interaction (event-based)
  - Only for specific users (conditional rendering)
  - Via client-side JavaScript (DOM XSS)

---

## Final Mental Checklist

Before calling something XSS, always ask:
1. Where does my input land in the HTML?
2. Is that location executable by the browser?
3. Does the JavaScript engine interpret it?

If the JS engine touches user input → XSS.
