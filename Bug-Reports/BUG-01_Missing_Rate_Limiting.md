# [BUG-01] Missing Rate Limiting Allows Brute Force on Auth Endpoints

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-01 |
| **Title** | Missing Rate Limiting Allows Brute Force on Auth Endpoints |
| **Severity** | Critical |
| **Priority** | P1 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-041, TC-042, TC-093, TC-117 |

## Description
The `/api/auth/login`, `/api/auth/signup`, and `/api/auth/verify-email` endpoints do not enforce any rate limiting. An attacker can send thousands of requests per minute to brute-force user passwords or overwhelm the server, leading to credential compromise or a Denial of Service (DoS) condition.

## Steps to Reproduce
1. Start the backend server.
2. Using Postman Runner or JMeter, send **500 consecutive** `POST` requests to `/api/auth/login` with an incorrect password for an existing user email.
3. Observe all server responses.

## Expected Result
After a threshold of failed attempts (e.g., 5 per IP per minute), the server should respond with `HTTP 429 Too Many Requests`. Subsequent requests from the same IP should be blocked or delayed.

## Actual Result
All 500 requests receive `HTTP 400 "Invalid credentials"`. No throttling, blocking, or account lockout is triggered at any point.

## Root Cause
No rate-limiting middleware (e.g., `express-rate-limit`) is registered in `index.js` or on any individual route.

## Recommended Fix
```js
// index.js
import rateLimit from "express-rate-limit";

const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 10,
    message: { success: false, message: "Too many attempts. Please try again later." }
});

app.use("/api/auth/login", authLimiter);
app.use("/api/auth/forgot-password", authLimiter);
```

## Environment
- **Endpoint**: `POST /api/auth/login`, `POST /api/auth/forgot-password`, `POST /api/auth/verify-email`
- **File**: `backend/index.js`
- **Project**: Authentication System Backend API
