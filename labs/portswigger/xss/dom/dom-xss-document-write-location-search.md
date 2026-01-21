# DOM XSS – document.write with location.search

## Lab
DOM XSS in document.write sink using source location.search

## Source
window.location.search (search parameter)

## Sink
document.write()

## Vulnerable Code
document.write('<img src="/resources/images/tracker.gif?searchTerms=' + query + '">');

## Context
HTML attribute context (img src)

## Payload
"><script>alert(1)</script>

## Explanation
User input is read directly from the browser URL using location.search.
The value is concatenated into an HTML string and written to the DOM using
document.write(), creating an img tag with attacker-controlled content.

By breaking out of the src attribute and closing the tag, arbitrary HTML and
JavaScript can be injected and executed in the browser.

## Key Lesson
DOM-based XSS occurs entirely in the browser.
View Source may appear safe, but JavaScript sinks like document.write()
can dynamically create vulnerable HTML contexts.
