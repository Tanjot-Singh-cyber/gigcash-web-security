## Report edihasaj/tuspyserver

### Target Overview

| Field | Detail |
|---|---|
| Repository | https://github.com/edihasaj/tuspyserver |
| Description | FastAPI library implementing TUS resumable upload protocol |
| Language | Python (FastAPI) |
| Stars | 35 |
| Contributors | 1 |
| Last Commit | December 2022 |
| PyPI Package | tuspyserver |

### Vulnerability (Investigated, Not Confirmed Exploitable)

**Title:** Path Traversal via unvalidated UUID parameter — mitigated by HTTP layer  
**CWE:** CWE-22 — Path Traversal (theoretical)  
**Severity:** Not applicable — not directly exploitable  
**Issue:** https://github.com/edihasaj/tuspyserver/issues/83  
**Status:** Closed as completed (defense-in-depth discussion)  

### Affected Files

- `src/tuspyserver/file.py` — `path` property
- `src/tuspyserver/routes/core.py` — HEAD, PATCH routes
- `src/tuspyserver/routes/termination.py` — DELETE route
- `src/tuspyserver/routes/creation.py` — Upload-Concat header parsing

### Vulnerable Code (Code Level)

```python
# file.py
@property
def path(self) -> str:
    return os.path.join(self._options.files_dir, f"{self.uid}")
```

`os.path.join()` does not resolve `..` sequences. The uid comes from the URL path parameter with no validation:

```python
# termination.py
@router.delete("/{uuid}")
async def extension_termination_route(uuid: str, ...):
    file = TusUploadFile(uid=uuid, options=file_options)
    file.delete(uuid)  # uuid never validated
```

Python confirmed path escapes `files_dir`:
```python
os.path.normpath(os.path.join("/app/uploads", "../config.py"))
# returns: /app/config.py
# normalized path startswith check: False
```

### Why Not Exploitable in Practice

During live testing, both uvicorn and Hypercorn normalize `..` sequences in URL paths before routing, per RFC 3986:

```
DELETE /files/../target.txt HTTP/1.1
→ Server receives: DELETE /target.txt HTTP/1.1
→ No route match → 404
```

The Upload-Concat header parsing also accidentally strips traversal:
```python
elif "/" in url_or_uid:
    path_parts = [p for p in url_or_uid.split("/") if p]
    uid = path_parts[-1]  # extracts "target.txt", drops ".."
```

### Conclusion

Code-level vulnerability exists. Standard HTTP servers mitigate it at the routing layer. Reported as a hardening issue — adding strict UUID format validation (`^[a-f0-9]{32}$`) would eliminate the theoretical risk entirely.

### Disclosure Timeline

| Date | Event |
|---|---|
| 2026-05-15 | Path traversal pattern identified via static code review |
| 2026-05-15 | Live testing — uvicorn blocks traversal via URL normalization |
| 2026-05-15 | Hypercorn tested — same result |
| 2026-05-15 | Issue #83 submitted with accurate assessment |
| 2026-05-15 | Developer reviewed and closed as completed |

### Key Lesson

Code-level vulnerability ≠ exploitable vulnerability. Static analysis finds candidates. Live testing confirms reality. Reporting accurately when a finding is weaker than expected is the correct approach.

---
