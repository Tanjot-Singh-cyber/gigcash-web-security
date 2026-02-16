Endpoint: GET /api/getStudentActivity/{StuID}/undefined

Findings:
- Authenticated via session cookie
- StuID validated against session owner
- Unchanged request → 200 + empty data
- Modified StuID → 403 Forbidden ("Invalid student")

Conclusion:
- Proper authorization enforced
- No IDOR observed
