# [BUG-05] No Email Format Validation on Signup and Login

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-05 |
| **Title** | No Email Format Validation on Signup |
| **Severity** | Major |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-014, TC-015, TC-016, TC-017 |

## Description
The `POST /api/auth/signup` endpoint accepts any string value as an email address without validating its format. There is no regex validator, Mongoose `match` option, or third-party validation library (e.g., `express-validator`) applied to the `email` field. Malformed emails such as `testexample.com` (no `@`), `test@.com`, `test@@example.com`, and emails with leading/trailing whitespace are all accepted and stored in the database as-is.

## Steps to Reproduce
1. Send a `POST` request to `/api/auth/signup` with the payload:
   ```json
   { "name": "Test", "email": "testexample.com", "password": "Test1234" }
   ```
2. Observe the response status and the newly created database document.

## Expected Result
The server should return `400 Bad Request` with a descriptive message such as `"Please provide a valid email address."` and reject the registration.

## Actual Result
The server returns `201 Created`. The malformed email `testexample.com` is stored in the database without any format check applied.

## Additional Cases
| Input | Should Fail | Actually Fails |
|-------|------------|----------------|
| `testexample.com` | ✅ Yes | ❌ No — stored |
| `test@.com` | ✅ Yes | ❌ No — stored |
| `test@@example.com` | ✅ Yes | ❌ No — stored |
| ` test@example.com ` (with spaces) | ✅ Yes (or trim) | ❌ No — stored with spaces |

## Root Cause
The Mongoose `userSchema` defines `email` as `{ type: String, required: true, unique: true }` with no `match` regex. The controller only checks `!email` (truthiness). No trimming or format normalization is applied.

## Environment
- **Endpoint**: `POST /api/auth/signup`
- **File**: `backend/controllers/auth.controller.js`, `backend/models/user.model.js`
- **Project**: Authentication System Backend API
