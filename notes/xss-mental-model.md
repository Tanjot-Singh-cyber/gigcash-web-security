# XSS Practical Mental Model (Context → Escaping → Sink → Execution)

This file is a practical decision map.
No definitions. No theory dumps.
Only: what to check, what to do, what NOT to do, and why.

--------------------------------------------------

STEP 1 — WHERE does my input land?

1) HTML BODY CONTEXT
Example:
<div>USER_INPUT</div>

Think:
- Browser parses HTML directly

Do:
- HTML tag injection
- Script-capable elements

Don’t:
- Think about JS escaping
- Think about eval

Why:
- HTML is parsed immediately by the browser

--------------------------------------------------

2) HTML ATTRIBUTE CONTEXT
Example:
<a href="USER_INPUT">
<img src="USER_INPUT">

Think:
- Attribute semantics matter

Do:
- Identify attribute name
- Think protocol abuse
- Think event handlers

Don’t:
- Blindly try <script>
- Assume encoding = safe

Why:
- Execution depends on attribute behavior, not just HTML

--------------------------------------------------

3) JAVASCRIPT STRING CONTEXT
Example:
var x = 'USER_INPUT';

Think:
- JS strings are inert by default

Do:
- Check quote type (single / double)
- Check escaping (' " \)
- Track where variable flows

Don’t:
- Try </script> randomly
- Assume strings execute

Why:
- Strings only execute if interpreted later

--------------------------------------------------

4) JAVASCRIPT EXPRESSION CONTEXT
Example:
doSomething(USER_INPUT)

Think:
- Immediate evaluation by JS engine

Do:
- Minimal valid JS expressions

Don’t:
- Try string breakouts
- Think HTML

Why:
- Expressions are evaluated immediately

--------------------------------------------------

STEP 2 — CAN I BREAK OUT?

Check escaping:

' or " escaped  → string breakout dead  
\ escaped        → escape tricks dead  
< > encoded      → HTML tags dead  

Action:
- If breakout works → classic JS injection
- If breakout fails → STOP and change strategy

--------------------------------------------------

STEP 3 — WHERE DOES THE DATA FLOW (SINK)?

A) SAFE DOM SINKS
innerText
textContent

Result:
- NOT exploitable

Why:
- No parsing, no execution

--------------------------------------------------

B) DOM PARSING SINKS
innerHTML
document.write

Execution path:
- Browser HTML parser

Think:
- HTML execution
- Attribute side effects

--------------------------------------------------

C) JAVASCRIPT EVALUATION SINKS
eval(x)
setTimeout(x)
setInterval(x)
Function(x)

Execution path:
- JavaScript engine re-parses string as code

Think:
- Pure JS execution
- Expression-level logic

--------------------------------------------------

D) URL-BASED SINKS
location.href = x
element.src = x
element.href = x

Execution path:
- Browser URL handler

Think:
- javascript: protocol
- data: URLs

--------------------------------------------------

STEP 4 — IMMUTABLE RULES

- Escaping ≠ safety
- Strings do NOT execute by themselves
- Execution always requires an interpreter

Only three interpreters exist:
1) JavaScript engine
2) HTML parser
3) URL handler

If input reaches NONE of these → NO XSS

--------------------------------------------------




