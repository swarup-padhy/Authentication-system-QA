# [BUG-06] Unverified Users Can Log In Without Email Verification

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-06 |
| **Title** | Unverified Users Can Log In Without Email Verification |
| **Severity** | Major |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-032 |

## Description
The `POST /api/auth/login` endpoint does not check the `isVerified` flag on the user document before issuing a JWT and granting access. A newly registered user who has never clicked the verification link in their email can log in immediately with a valid session token, bypassing the email verification step entirely.

## Steps to Reproduce
1. Send a `POST` request to `/api/auth/signup` with a valid name, email, and password. Do **not** submit the verification code.
2. Immediately send a `POST` request to `/api/auth/login` with the same email and password.
3. Observe the response.

## Expected Result
The server should return `403 Forbidden` or `401 Unauthorized` with a message such as `"Please verify your email address before logging in."` and withhold the JWT cookie.

## Actual Result
The server returns `200 OK` with `"Logged in successfully"` and sets a valid JWT cookie, granting full authenticated access to the unverified user.

## Root Cause
The `login` controller in `auth.controller.js` contains no `isVerified` check after retrieving the user from the database. The flow proceeds directly from `bcryptjs.compare()` to `generateTokenAndSetCookie()` without inspecting the `isVerified` field.

```js
// Missing check — should be here:
// if (!user.isVerified) {
//   return res.status(403).json({ message: "Please verify your email." });
// }
generateTokenAndSetCookie(res, user._id); // issued immediately
```

## Environment
- **Endpoint**: `POST /api/auth/login`
- **File**: `backend/controllers/auth.controller.js` (line 104)
- **Project**: Authentication System Backend API
