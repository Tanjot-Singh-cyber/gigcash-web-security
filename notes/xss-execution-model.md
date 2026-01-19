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
