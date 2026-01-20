# Reflected XSS – HTML Context

## Input Point
Search parameter is reflected in the response.

## Context Analysis
User-controlled input is reflected inside the HTML body as text
without any output encoding.

## Reasoning
Because the browser parses reflected input as HTML,
injecting a script tag results in JavaScript execution.

## Proof
Injected <script>alert(1)</script> and observed execution in the browser.

## Fix
Properly HTML-encode user input before rendering it in the response.
