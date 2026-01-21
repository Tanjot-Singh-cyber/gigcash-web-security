# Reflected XSS – Attribute Context (Double Quotes)

## Lab
Reflected XSS into attribute with angle brackets HTML-encoded

## Vulnerable Context
User input is reflected inside an HTML attribute value wrapped in double quotes.

Example:
<input value="USER_INPUT">
Context: HTML attribute (double-quoted)

## Data Flow
Query parameter -> attribute value in HTML response

## Encoding Behavior
Angle brackets (< >) are HTML-encoded.
Double quotes are not encoded.
This allows attribute value manipulation.

## Why Script Tags Don’t Work
<script> tags are blocked because angle brackets are encoded.
Breaking into HTML context is not possible.

## Exploitation Strategy
Break out of the attribute value using a double quote
and inject an event handler.

## Example Payload
" autofocus onfocus=alert(1) x="

## Execution
Payload breaks out of the value attribute.
A new event handler is injected.
JavaScript executes when the input gains focus.

## Key Lesson
When user input is placed inside a double-quoted attribute:
- Encoding matters more than reflection
- Event handlers are often the execution vector
- Attribute context requires different payload logic than HTML context

