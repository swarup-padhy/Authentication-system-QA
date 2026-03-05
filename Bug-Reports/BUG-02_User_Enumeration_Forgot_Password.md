# [BUG-02] User Enumeration Vulnerability in Forgot Password

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-02 |
| **Title** | User Enumeration Vulnerability in Forgot Password |
| **Severity** | Major |
| **Priority** | P1 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-060 |

## Description
The `/api/auth/forgot-password` endpoint returns a distinct error message (`"User not found"`) when an unregistered email is submitted. This allows an attacker to systematically determine which email addresses are registered in the system by observing the difference between success and failure responses.

## Steps to Reproduce
1. Send a `POST` request to `/api/auth/forgot-password` with an **unregistered** email:
   ```json
   { "email": "doesnotexist@example.com" }
   ```
2. Observe the response body and status code.
3. Repeat with a **registered** email and compare the responses.

## Expected Result
Both registered and unregistered emails should return the **same** generic response:
- `HTTP 200 OK`
- Body: `{ "success": true, "message": "If this email is registered, a reset link has been sent." }`

## Actual Result
- Unregistered email → `HTTP 400 "User not found"` (exposes non-registration)
- Registered email → `HTTP 200 "Password reset link sent to your email"`

## Root Cause
```js
// auth.controller.js — forgotPassword()
if (!user) {
    return res.status(400).json({ success: false, message: "User not found" }); // BUG
}
```
The controller reveals the exact lookup result to the client.

## Recommended Fix
```js
if (!user) {
    // Return generic response regardless of whether user exists
    return res.status(200).json({ success: true, message: "If this email is registered, a reset link has been sent." });
}
```

## Environment
- **Endpoint**: `POST /api/auth/forgot-password`
- **File**: `backend/controllers/auth.controller.js` (line 133–135)
- **Project**: Authentication System Backend API
