# Reflected XSS – JavaScript String (Single Quotes)

## Lab
Reflected XSS into a JavaScript string with angle brackets HTML-encoded

## Vulnerable Code
var searchTerms = 'USER_INPUT';

## Payload
';alert(1);//

## Explanation
User input is reflected inside a single-quoted JavaScript string.
The payload closes the string, executes JavaScript, and comments out the remainder.
