# [BUG-09] No Custom 404 Handler — Express Returns HTML Error Page

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-09 |
| **Title** | No Custom 404 Handler — Express Returns HTML Error Page |
| **Severity** | Minor |
| **Priority** | P3 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-089 |

## Description
When a request is made to a non-existent route under `/api/auth/` (e.g., `GET /api/auth/fake-route`), Express has no registered fallback handler and returns its default HTML error page: `Cannot GET /api/auth/fake-route`. This breaks the API contract — a REST API should always return JSON responses, even for 404 errors. The HTML response can also reveal that the server is running Express.

## Steps to Reproduce
1. Start the backend server.
2. Send a `GET` request to `http://localhost:5000/api/auth/nonexistent`.
3. Observe the response body and `Content-Type` header.

## Expected Result
The server should return `HTTP 404` with a JSON body:
```json
{ "success": false, "message": "Route not found" }
```
and `Content-Type: application/json`.

## Actual Result
Express returns an HTML page with the text `Cannot GET /api/auth/nonexistent` and `Content-Type: text/html`. This is not a valid JSON API response.

## Root Cause
No catch-all 404 middleware is registered in `index.js` after the route definitions:

```js
// Missing in index.js:
app.use((req, res) => {
    res.status(404).json({ success: false, message: "Route not found" });
});
```

## Environment
- **Endpoint**: Any undefined route under `/api/auth/`
- **File**: `backend/index.js`
- **Project**: Authentication System Backend API
