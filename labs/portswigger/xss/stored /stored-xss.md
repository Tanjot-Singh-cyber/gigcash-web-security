# Stored XSS – Comment Function

## Vulnerability Type
Stored Cross-Site Scripting (XSS)

## Vulnerable Input
Comment / message input field where user input is saved on the server

## Payload Used
<script>alert(1)</script>

## What Happened
The application stored user input without sanitization and later displayed it inside an HTML page.

## Why It Worked
User-controlled input was inserted into the HTML response without output encoding, so the browser interpreted it as executable JavaScript.

## Impact
Any user who views the affected page will execute the attacker’s script in their browser.

## Notes
This vulnerability was identified and exploited using manual testing without automated scanning.
