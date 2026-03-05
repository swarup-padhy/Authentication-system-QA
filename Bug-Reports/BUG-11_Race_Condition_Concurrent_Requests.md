# [BUG-11] Race Condition on Concurrent Verify-Email and Reset-Password Requests

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-11 |
| **Title** | Race Condition Allows Concurrent Requests to Consume Single-Use Tokens |
| **Severity** | Major |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-109, TC-110 |

## Description
The `POST /api/auth/verify-email` and `POST /api/auth/reset-password/:token` endpoints use a non-atomic read-then-write pattern. The controller first queries for the user via `findOne()`, then performs separate business logic, and finally saves via `user.save()`. Under concurrent execution, multiple simultaneous requests with the same valid token can all pass the `findOne()` check before any of them clears the token. This means a single-use token (verification code or password reset token) can be consumed more than once, breaking the one-time-use guarantee.

## Steps to Reproduce (Verify-Email)
1. Register a new user and obtain the 6-digit verification code.
2. Using a parallel request tool (e.g., Postman parallel runner, `Promise.all` in Node), send **2 simultaneous** `POST` requests to `/api/auth/verify-email` with the same code.
3. Observe both response codes.

## Expected Result
Only one request should return `200 OK "Email verified successfully"`. The second should return `400 "Invalid or expired verification code"` because the token was consumed by the first.

## Actual Result
Under concurrent load, both requests may find the user with the valid token before either clears it. Both requests may return `200 OK`, consuming the token twice and calling `sendWelcomeEmail` twice.

## Root Cause
The controller uses two separate database operations — `findOne` followed by `save` — with no atomicity guarantee:

```js
// Non-atomic — race condition exists:
const user = await User.findOne({ verificationToken: code, ... });
user.isVerified = true;
user.verificationToken = undefined;
await user.save();

// Atomic fix using findOneAndUpdate:
const user = await User.findOneAndUpdate(
    { verificationToken: code, verificationTokenExpiresAt: { $gt: Date.now() } },
    { $set: { isVerified: true }, $unset: { verificationToken: "", verificationTokenExpiresAt: "" } },
    { new: true }
);
```

The same pattern exists in the `resetPassword` controller.

## Environment
- **Endpoints**: `POST /api/auth/verify-email`, `POST /api/auth/reset-password/:token`
- **File**: `backend/controllers/auth.controller.js`
- **Project**: Authentication System Backend API
