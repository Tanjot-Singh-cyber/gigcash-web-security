# SQL Injection – Master Mental Model

## 1. What SQL Injection Is

SQL Injection occurs when user-controlled input is included in a SQL query
and interpreted as SQL logic instead of data.

The vulnerability appears when untrusted input crosses the **data–code boundary**
inside a SQL query.

---

## 2. Backend Execution Flow

Typical SQL query flow:

Browser → Server → Database

1. Browser sends request with user input
2. Backend constructs a SQL query
3. Query is sent to the database
4. Database executes the query

The vulnerability occurs **during query construction**, when user input becomes
part of the query structure.

---

## 3. SQL Sink

The **SQL sink** is the point where the database executes the query.

If user input reaches this sink without proper separation from query logic,
the database may interpret it as SQL code.

Examples include database execution functions used by applications.

---

## 4. Why String Concatenation Is Dangerous

Building queries using string concatenation mixes data with query logic.

Example structure:

```sql
SELECT * FROM users WHERE username = 'USER_INPUT';
```

If the input escapes the string boundary, it can become part of the SQL logic
and change the meaning of the query.

---

## 5. SQL Injection Contexts

SQL Injection depends on where input appears inside the query.

### String context

```sql
SELECT * FROM users WHERE username = 'USER_INPUT';
```

### Numeric context

```sql
SELECT * FROM products WHERE id = USER_INPUT;
```

### Identifier context

```sql
SELECT USER_INPUT FROM users;
```

Each context affects how input can alter the query.

---

## 6. Why Input Filtering Fails

Blacklisting characters or keywords does not reliably prevent SQL Injection.

Reasons include:

- SQL syntax is complex
- Databases support many encodings
- Attackers can bypass filters
- Context changes how characters behave

Filtering tries to predict attacker behavior, which is unreliable.

---

## 7. Blind SQL Injection

Blind SQL Injection occurs when a SQL injection vulnerability exists,
but the application does **not return query results or database errors**.

The SQL query still executes, but the attacker cannot directly see the output.

Blind does not mean safe. It only means the output channel is hidden.

---

## 8. Behavioral Feedback

When output is hidden, attackers observe **application behavior**
to infer query execution.

Examples include:

- Page loads vs page errors
- Login success vs failure
- Redirect vs no redirect
- Response time differences
- Minor content changes

These behavioral differences become the feedback channel.

---

## 9. Boolean-Based Blind SQL Injection

Boolean-based blind SQL Injection relies on **true/false conditions**.

If a condition evaluates to true, the application behaves one way.
If it evaluates to false, the application behaves differently.

The change in behavior confirms query execution.

---

## 10. Time-Based Blind SQL Injection

Time-based blind SQL Injection relies on **response delays**.

The database is forced to delay execution under certain conditions.

A slower response indicates the injected SQL logic was executed.

Response time becomes the feedback channel.

---

## 11. Blind SQL Injection Is Not a Different Bug

Blind SQL Injection is not a separate vulnerability.

The root cause is identical to normal SQL Injection:

- User input reaches a SQL sink
- The database interprets the input as SQL logic

The only difference is **how the attacker observes the result**.

---

## 12. Root Cause

All SQL Injection vulnerabilities come from the same issue:

Untrusted input crossing the **data–code boundary** inside a SQL query.

---

## 13. Proper Fix: Parameterized Queries

Parameterized queries separate SQL logic from user input.

The SQL statement is defined first, and user input is passed separately.

The database always treats the input as **data**, never executable SQL.

This prevents input from modifying query structure.

---

## 14. SQL Injection vs XSS

Both vulnerabilities come from the same core problem:

Untrusted input reaching an execution sink.

**SQL Injection**
- Executes in the database
- Occurs during query execution

**XSS**
- Executes in the browser
- Occurs during page rendering

---

## 15. One-Line Summary

SQL Injection occurs when attacker-controlled input becomes part of SQL
query logic instead of remaining data.
