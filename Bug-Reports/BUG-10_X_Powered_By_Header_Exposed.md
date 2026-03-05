# [BUG-10] X-Powered-By: Express Header Exposed in All Responses

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-10 |
| **Title** | `X-Powered-By: Express` Header Exposed in All Responses |
| **Severity** | Minor |
| **Priority** | P3 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-087 |

## Description
Every HTTP response from the server includes the `X-Powered-By: Express` header. This header is set by Express by default and discloses the server technology stack to any client or attacker inspecting response headers. Exposing technology information aids fingerprinting and targeted exploit selection during reconnaissance.

## Steps to Reproduce
1. Send any request to any endpoint (e.g., `POST /api/auth/login`).
2. Inspect the response headers in Postman or browser DevTools.
3. Locate the `X-Powered-By` header.

## Expected Result
The `X-Powered-By` header should be absent from all responses. Technology stack details must not be disclosed to clients.

## Actual Result
The response header `X-Powered-By: Express` is present in all API responses.

## Root Cause
Express adds this header by default. It has not been disabled in `index.js`:

```js
// Missing in index.js:
app.disable("x-powered-by");
// OR use helmet which handles this and more:
// app.use(helmet());
```

## Environment
- **File**: `backend/index.js`
- **Affected Endpoints**: All (`/api/auth/*`)
- **Project**: Authentication System Backend API
