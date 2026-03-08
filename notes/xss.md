# XSS – Master Mental Model

## 1. Core Rule

Cross-Site Scripting (XSS) occurs when attacker-controlled input is interpreted as executable JavaScript by the browser.

Important distinction:

Input ≠ vulnerability  
Execution context = vulnerability

User input alone is harmless.  
XSS happens only when that input reaches an executable context.

---

## 2. Backend Flow (Where XSS Starts)

Typical application flow:

Request → Server → Response → Browser

1. User submits input
2. Server processes the input
3. Server includes the input in the HTML response
4. Browser renders the response

If the browser interprets the input as code → XSS.

---

## 3. Reflected vs Stored XSS

### Reflected XSS

Input is returned immediately in the response.

Flow:

Request → Server → Response → Browser

Characteristics:

- Input is not stored
- Payload must be delivered in the request
- Only affects users who trigger the request

Example: search results reflecting user input.

---

### Stored XSS

Input is stored on the server before execution.

Flow:

Request → Server → Database  
Database → Server → Response → Browser

Characteristics:

- Payload persists
- Affects multiple users
- Executes whenever stored content is rendered

Examples:

- Comments
- Profile fields
- Forum posts

---

## 4. Browser Execution Model

The browser has three interpreters that can execute attacker input:

1. HTML Parser
2. JavaScript Engine
3. URL Handler

XSS occurs when input reaches any of these interpreters unsafely.

---

## 5. Common Executable Contexts

User input becomes executable when inserted into:

### Script blocks

```
<script> USER_INPUT </script>
```

### Event handler attributes

```
onclick
onload
onerror
onfocus
```

### javascript URLs

```
<a href="javascript:alert(1)">
```

### JavaScript strings

```
var x = "USER_INPUT"
```

### DOM sinks

```
innerHTML
document.write
outerHTML
```

---

## 6. Safe vs Dangerous DOM Sinks

### Safe (no execution)

```
textContent
innerText
```

These render text without parsing.

### Dangerous (execution possible)

HTML parsing:

```
innerHTML
document.write
```

JavaScript evaluation:

```
eval()
setTimeout()
setInterval()
Function()
```

URL execution:

```
location.href
element.src
element.href
```

---

## 7. XSS Testing Methodology

Never guess payloads.

Follow this process:

1. Send harmless input
2. Observe where input appears
3. Identify transformations
4. Determine execution context
5. Craft payload based on context

Payloads are a result of analysis, not creativity.

---

## 8. Identifying Input Sources

### Reflected source

- Input appears immediately
- Visible in page source

### Stored source

- Input persists after refresh
- Appears for other users

### DOM source

- Comes from URL, fragment, or browser storage
- Only visible in runtime DOM

---

## 9. Execution Timing

XSS may execute:

Immediate  
- page load

On interaction  
- click  
- hover  
- focus

Later  
- stored content rendered later

Conditional  
- specific users or roles

No popup does not mean no vulnerability.

---

## 10. Context Determines Exploitability

Payload success depends entirely on where input lands.

### HTML body

```
<div>USER_INPUT</div>
```

### HTML attribute

```
<a href="USER_INPUT">
```

### JavaScript string

```
var x = "USER_INPUT"
```

### JavaScript expression

```
doSomething(USER_INPUT)
```

Context determines:

- escaping needed
- breakout strategy
- payload format

---

## 11. Escaping and Encoding

Applications often transform input.

HTML encoding:

```
< → &lt;
> → &gt;
```

JavaScript escaping:

```
' → \'
" → \"
```

Filtering may also remove tags or characters.

Important rule:

Encoding must occur at output, based on context.

Encoding at input time fails because data may be reused elsewhere.

---

## 12. Why Payloads Fail

If a payload does not execute, usually one of these is wrong:

- source misunderstood
- context misidentified
- transformations ignored
- sink not executable

Payload syntax is rarely the real problem.

---

## 13. Final Mental Checklist

Before confirming XSS:

1. Where does my input land?
2. Is the context executable?
3. Which interpreter processes it?
4. Can I escape the context?
5. Does the data reach an execution sink?

If the browser never interprets the input as code → no XSS.

---

## 14. One-Line Summary

XSS occurs when attacker-controlled input reaches a browser execution context without proper output encoding.
