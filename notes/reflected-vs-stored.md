# Reflected XSS vs Stored XSS (Backend Perspective)

## Reflected XSS

- User input is sent in an HTTP request.
- The server processes the input but does not store it.
- The input is reflected directly in the HTML response.
- If output encoding is missing, the browser executes it as JavaScript.

Backend flow:
Request → Server → Response → Browser

## Stored XSS

- User input is sent to the server and saved in a database.
- The stored data is later retrieved and included in responses.
- Every user who loads the affected page receives the payload.
- Missing output encoding causes the browser to execute it.

Backend flow:
Request → Server → Database  
Database → Server → Response → Browser

## Key Difference

- Reflected XSS data lives only for one request.
- Stored XSS data persists and affects multiple users.
- The vulnerability and fix are the same: missing output encoding.

