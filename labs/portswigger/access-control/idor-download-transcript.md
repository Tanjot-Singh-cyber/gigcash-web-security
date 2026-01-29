Lab: Insecure Direct Object Reference – Download transcript

Vulnerability:
Broken Access Control (IDOR – horizontal privilege escalation)

Endpoint:
GET /download-transcript/{id}.txt

Auth model:
- Authentication: session cookie
- Authorization: missing object ownership validation

Observation:
The transcript ID is a predictable numeric value passed in the URL.
The server checks if the user is authenticated but does not verify
whether the requested transcript belongs to the logged-in user.

Proof:
1. Logged in as a normal user.
2. Downloaded own transcript.
3. Intercepted request:
   GET /download-transcript/3.txt
4. Changed the ID to another value:
   GET /download-transcript/1.txt
5. Server returned another user’s private chat transcript.

Impact:
- Unauthorized access to other users’ private data
- Leakage of sensitive information (credentials in chat)
- Complete failure of object-level authorization

Root cause:
Server trusts user-controlled object IDs and enforces authentication
without enforcing per-object authorization.

Fix:
Validate ownership of the requested object before returning it.

Key takeaway:
Authentication ≠ Authorization.
Every object access must be explicitly authorized.
