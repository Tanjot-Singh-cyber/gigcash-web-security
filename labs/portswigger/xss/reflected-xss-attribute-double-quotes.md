# Reflected XSS – Attribute Context (Double Quotes Encoded)

## Lab
Reflected XSS into attribute with angle brackets HTML-encoded

## Reflection Point
User input is reflected inside:

```html
<input type="text" placeholder="Search the blog..." name="search" value="USER_INPUT">
