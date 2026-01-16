# Backend Flow – Core Security Concepts

## 1. HTTP Request
An HTTP request is sent from the client to the server.
All data in a request is attacker-controlled (query params, body, headers, cookies).

## 2. Server-side Routing
The server maps the request to a route/controller.
At this stage, input is just data and not dangerous by itself.

## 3. Backend Logic
The backend processes input, applies logic, and may store or transform data.
No vulnerability exists here unless the input is later misused.

## 4. Sink
A sink is a sensitive operation where data is interpreted or executed.
Examples:
- HTML rendering (XSS)
- SQL query execution (SQL Injection)
- OS command execution (Command Injection)

Vulnerabilities occur when user input reaches a sink unsafely.

## 5. Context
Context defines how the sink interprets the data.
Examples:
- HTML body
- HTML attribute
- JavaScript string
- SQL string

Same input behaves differently in different contexts.

## 6. Cross-Site Scripting (XSS)
XSS occurs when user input is included in a response and interpreted as executable code by the browser.
Execution happens at render time in the browser.

## 7. Reflected vs Stored XSS
Reflected XSS:
- Input is reflected immediately in the response
- Not stored

Stored XSS:
- Input is stored in a database
- Executed when data is later retrieved and rendered

## 8. Payload
A payload is a proof-of-concept used to demonstrate a vulnerability.
The vulnerability exists even if no payload works.

## 9. alert()
alert() is only used to prove execution.
It is not the real impact of XSS.

## 10. Validation vs Encoding
Input validation checks format and correctness.
Output encoding converts dangerous characters at the sink.

Rule:
Validate early, encode late.

## 11. Why Encoding at Input Time Fails
Data may be reused in multiple contexts.
Encoding must be done at output time, based on context.

## 12. Client-side Checks
Browser-side validation improves UX but does not provide security.
Security controls must exist on the server.

## 13. Core Principle
Injection vulnerabilities occur when untrusted input is treated as trusted code at a sink.

## 14. Render Time
Render time is the stage when the browser processes the HTTP response.
At render time, the browser:
- Parses HTML
- Builds the DOM
- Interprets JavaScript
- Decides whether input is text or executable code

XSS occurs at render time, not when input is submitted or stored.

## 15. Parse Time
Parse time is when the browser reads and interprets the structure of the HTML and JavaScript.
During parsing, the browser determines:
- Tag boundaries
- Attributes
- Script blocks

Improperly encoded input can change how parsing occurs, leading to XSS.

## 16. Execution Context
Execution context defines where and how code is executed.
Examples:
- HTML context
- JavaScript context
- Attribute context

The same input can be safe in one context and dangerous in another.
