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
