# Stored XSS – Anchor href Attribute (JavaScript URL)

## Lab
Stored XSS into anchor href attribute with double quotes HTML-encoded

## Reflection Point
User-controlled input from the **Website** field is stored and rendered inside:

```html
<a id="author" href="USER_INPUT">Author name</a>
