# [BUG-04] No Password Strength Validation on Signup or Password Reset

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-04 |
| **Title** | No Password Strength Validation on Signup or Password Reset |
| **Severity** | Minor |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-018, TC-019, TC-020, TC-069 |

## Description
The `POST /api/auth/signup` and `POST /api/auth/reset-password/:token` endpoints accept any non-empty string as a valid password with no minimum length, complexity, or strength enforcement. This allows users to set trivially guessable passwords (e.g., `"1"`, `"abc"`, `"password"`), significantly weakening account security.

## Steps to Reproduce
1. Send a `POST` request to `/api/auth/signup` with a minimal password:
   ```json
   { "name": "Test User", "email": "test@example.com", "password": "1" }
   ```
2. Observe the response status.

## Expected Result
`HTTP 400 Bad Request` with a descriptive message:
```json
{ "success": false, "message": "Password must be at least 8 characters and include a number and a special character." }
```

## Actual Result
`HTTP 201 Created`. The one-character password `"1"` is hashed by bcryptjs and stored in the database without any validation.

## Root Cause
Neither the controller nor the Mongoose schema defines any password validation:
```js
// auth.controller.js — signup() — no password validation before hashing
const hashedPassword = await bcryptjs.hash(password, 10); // immediately hashed
```

## Recommended Fix
Add a validation check before hashing:
```js
const passwordRegex = /^(?=.*[0-9])(?=.*[!@#$%^&*]).{8,}$/;
if (!passwordRegex.test(password)) {
    return res.status(400).json({ success: false, message: "Password must be 8+ chars with a number and special character." });
}
```
Apply the same check in the `resetPassword` controller.

## Environment
- **Endpoints**: `POST /api/auth/signup`, `POST /api/auth/reset-password/:token`
- **File**: `backend/controllers/auth.controller.js` (line 28, line 171)
- **Project**: Authentication System Backend API
