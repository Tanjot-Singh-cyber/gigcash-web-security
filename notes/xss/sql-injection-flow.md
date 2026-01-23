# SQL Injection – Backend Flow

## 1. What is SQL Injection
SQL Injection occurs when user-controlled input is included in a SQL query
and interpreted as SQL logic instead of data.

## 2. SQL Injection Flow
Browser sends request with user input.
Backend builds a SQL query using that input.
Database executes the query.

The vulnerability occurs during query construction, not execution.

## 3. SQL Sink
The SQL sink is the point where the database executes the constructed query.
If user input reaches this sink without proper separation from query logic,
SQL Injection is possible.

## 4. Why String Concatenation is Dangerous
When input is concatenated into a query string, it can escape the data boundary
and become part of the SQL logic.

This allows attackers to change the meaning of the query.

## 5. Context in SQL Injection
SQL Injection depends on context:
- String context
- Numeric context
- Identifier context

Filtering characters like quotes fails because SQL syntax is complex
and context-dependent.

## 6. Why Validation Fails
Blacklisting characters or keywords is unreliable.
SQL Injection cannot be fixed with validation alone.

## 7. Parameterized Queries
Parameterized queries send SQL logic and user input separately to the database.
User input is always treated as data, never as executable SQL.

This is the correct fix for SQL Injection.

## 8. XSS vs SQL Injection
Both are injection vulnerabilities caused by the same root issue:
untrusted input crossing the data–code boundary at a sink.

XSS executes in the browser at render time.
SQL Injection executes in the database at query time.
