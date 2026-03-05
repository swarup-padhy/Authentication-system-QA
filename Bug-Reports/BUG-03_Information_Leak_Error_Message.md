# [BUG-03] Information Leak via Raw error.message in Catch Blocks

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-03 |
| **Title** | Information Leak via Raw `error.message` in Catch Blocks |
| **Severity** | Major |
| **Priority** | P1 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-026, TC-044, TC-027, TC-068 |

## Description
The `signup`, `login`, `forgotPassword`, and `resetPassword` controller functions pass the raw `error.message` string directly to the client in the JSON response body. Raw error messages from Mongoose, Node.js, or bcryptjs expose internal architecture details — including database schema information, connection string fragments, and third-party service error messages — to any user who triggers an unhandled exception.

## Steps to Reproduce
1. Stop the MongoDB service (or misconfigure `MONGO_URI` in `.env`).
2. Send a valid `POST` request to `/api/auth/login`:
   ```json
   { "email": "user@example.com", "password": "Password123" }
   ```
3. Observe the `message` field in the response body.

## Expected Result
`HTTP 500` with a sanitized, generic message:
```json
{ "success": false, "message": "An unexpected error occurred. Please try again later." }
```
The internal error should be logged server-side only.

## Actual Result
`HTTP 400` with the raw Mongoose error exposed to the client:
```json
{ "success": false, "message": "buffering timed out after 10000ms" }
```

## Root Cause
All catch blocks pass `error.message` verbatim to the response. Additionally, the status code is `400` even for server-side failures, which should be `500`:
```js
// Current (all controllers):
} catch (error) {
    res.status(400).json({ success: false, message: error.message }); // BUG
}
```

## Recommended Fix
```js
} catch (error) {
    console.error("Internal error:", error); // Log internally
    res.status(500).json({ success: false, message: "An unexpected error occurred." });
}
```

## Environment
- **File**: `backend/controllers/auth.controller.js` (lines 54–56, 117–119, 150–153, 181–184)
- **Affected Endpoints**: `POST /api/auth/signup`, `POST /api/auth/login`, `POST /api/auth/forgot-password`, `POST /api/auth/reset-password/:token`
- **Project**: Authentication System Backend API
