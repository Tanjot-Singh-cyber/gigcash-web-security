# Blind SQL Injection

## 1. What Blind SQL Injection Means
Blind SQL Injection occurs when user input reaches a SQL sink and is executed by the database,
but the application does not display query results or database errors in the response.

The vulnerability still exists, but direct output is not visible.

## 2. Execution Still Happens
In blind SQL Injection:
- The SQL query is executed by the database
- The data–code boundary is crossed
- Only the feedback channel is limited

Blind does not mean safe. It only means silent.

## 3. Feedback Through Application Behavior
Since no data or errors are shown, the attacker observes application behavior to infer execution.

Behavioral differences may include:
- Page load vs no load
- Login success vs failure
- Redirect vs no redirect
- Response time differences
- Minor content changes

These differences act as indirect feedback.

## 4. Boolean-Based Blind SQL Injection
Boolean-based blind SQL Injection relies on true or false conditions.

If a condition evaluates to true, the application behaves one way.
If it evaluates to false, the application behaves differently.

The difference in behavior confirms query execution.

## 5. Time-Based Blind SQL Injection
Time-based blind SQL Injection relies on response delays.

The database is forced to delay execution under certain conditions.
A slower response indicates that the injected logic was executed.

Response time becomes the feedback channel.

## 6. Why Blind SQL Injection Is Not a Different Bug
Blind SQL Injection is not a separate class of vulnerability.

The root cause is the same as normal SQL Injection:
- User input is treated as SQL logic instead of data
- Input crosses the data–code boundary at the SQL sink

Only visibility of results is different.

## 7. Core Principle
Blind SQL Injection proves vulnerabilities through inference rather than direct output.
The correct fix remains parameterized queries.
