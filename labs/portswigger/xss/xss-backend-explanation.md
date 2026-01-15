# XSS – Backend Flow Explanation

Cross-Site Scripting (XSS) happens when a web application takes user-controlled input and includes it in its response without handling it safely. The vulnerability is not about the payload itself, but about how the server processes and outputs the data.

## Request → Server → Response → Browser Flow

1. A user enters input on a website, such as in a search box or URL parameter.
2. The browser sends this input to the server as part of an HTTP request (for example, a GET or POST request).
3. The server receives the input and stores it in a variable.
4. The server then generates an HTML response and includes the user input somewhere in that response.
5. The browser receives the response and renders it.

If the input is treated as plain text, it is displayed safely. If it is treated as executable code, it runs in the browser.

## Where XSS Occurs

XSS occurs when:
- User input is reflected or stored by the server
- The server inserts that input into an executable context (HTML, JavaScript, or attributes)
- No proper output encoding is applied

For example, if the server responds with:

```html
<p>You searched for <script>alert(1)</script></p>
