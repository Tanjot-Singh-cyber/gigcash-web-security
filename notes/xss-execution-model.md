# XSS Execution Model

XSS occurs when attacker-controlled data is interpreted as executable code
by the browser, not when it is merely stored or reflected.

Key idea:
- Input source does not matter
- Execution context does

Browser behavior:
- textContent / innerText -> data rendered as text (safe)
- innerHTML / document.write / eval -> data parsed as code (dangerous)

Execution timing:
- On page load (automatic)
- On user interaction (click, hover, focus)
- Later (stored XSS)

Rule:
Events trigger execution, but sinks cause XSS.

## Executable Contexts

JavaScript can execute without <script> tags.

Common execution contexts:
- Event handler attributes (onerror, onclick, onload, etc.)
- javascript: URLs in href or src
- DOM insertion using innerHTML

Non-executable by default:
- data-* attributes
- class, id, title
- textContent / innerText

Payload choice depends on the execution context, not filters.

