# Reflected XSS – Search Function

## Vulnerability Type
Reflected Cross-Site Scripting (XSS)

## Vulnerable Input
Search parameter reflected in the HTML response

## Injection Context
HTML body context

## Payload Used
<script>alert(1)</script>

## Why It Worked
User input was reflected without output encoding, allowing injected JavaScript to execute.

