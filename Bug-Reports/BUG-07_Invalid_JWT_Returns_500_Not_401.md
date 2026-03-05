# [BUG-07] Tampered or Invalid JWT Returns HTTP 500 Instead of HTTP 401

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-07 |
| **Title** | Tampered or Invalid JWT Returns HTTP 500 Instead of HTTP 401 |
| **Severity** | Major |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-076, TC-126 |

## Description
The `verifyToken` middleware catches all JWT verification errors (expired, tampered signature, malformed, missing signature) in a generic `catch (error)` block and returns `HTTP 500 "Server error"`. This is semantically incorrect — a bad token is a **client-side authentication error** (401), not a **server-side error** (500). This also causes any monitoring/alerting system watching for 5xx errors to generate false positives on every tampered request.

## Steps to Reproduce
1. Log in and capture the JWT cookie value.
2. Tamper the cookie by modifying the signature portion (e.g., change the last few characters).
3. Send a `GET` request to `/api/auth/check-auth` with the tampered token in the `token` cookie.
4. Observe the HTTP response status code.

## Expected Result
The server should return `HTTP 401 Unauthorized` with the message `"Unauthorized - invalid token"`.

## Actual Result
The server returns `HTTP 500 "Server error"`. The `JsonWebTokenError` thrown by `jwt.verify()` is caught by the `catch` block which unconditionally returns 500.

## Root Cause
In `verifyToken.js`, the `catch` block returns a hardcoded 500 status regardless of the error type:

```js
// Current (incorrect):
} catch (error) {
    console.log("Error in verifyToken ", error);
    return res.status(500).json({ success: false, message: "Server error" });
}

// Fix — differentiate JWT errors from true server errors:
} catch (error) {
    if (error.name === "JsonWebTokenError" || error.name === "TokenExpiredError") {
        return res.status(401).json({ success: false, message: "Unauthorized - invalid token" });
    }
    return res.status(500).json({ success: false, message: "Server error" });
}
```

## Environment
- **Endpoint**: `GET /api/auth/check-auth`
- **File**: `backend/middleware/verifyToken.js` (line 15)
- **Project**: Authentication System Backend API
