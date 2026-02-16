## Authorization Testing (Read-only API)

**Target:** mycamu.co.in  
**Feature:** Student Activity

### Work Done
- Captured `GET /api/getStudentActivity/{StuID}/undefined`
- Established baseline with own `StuID`
- Modified `StuID` (single-digit change) to test ownership

### Observations
- Own `StuID` → `200 OK` with empty data
- Modified `StuID` → `403 Forbidden` (“Invalid student”)
- Backend validates `StuID` against authenticated session

### Conclusion
- Proper authorization enforced
- No IDOR or horizontal access issue found
- Endpoint secure despite weak input handling (`undefined` accepted)
