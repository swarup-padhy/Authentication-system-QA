# [BUG-12] verificationToken Field Has No Database Index

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-12 |
| **Title** | `verificationToken` Field Has No Database Index |
| **Severity** | Minor |
| **Priority** | P3 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-121 |

## Description
The `verificationToken` field in the `userSchema` is defined as a plain `String` with no `index: true` option. Every call to `POST /api/auth/verify-email` triggers a `User.findOne({ verificationToken: code })` query that performs a **full collection scan** (COLLSCAN) against the users collection. At scale, this causes slow query performance and unnecessary database load on a high-frequency endpoint.

## Steps to Reproduce
1. Connect to the MongoDB instance.
2. Run: `db.users.getIndexes()` in the Mongo shell.
3. Observe the index list.

## Expected Result
An index should exist on the `verificationToken` field, enabling fast `O(log n)` lookups. Additionally, `resetPasswordToken` should also be indexed for the same reason.

## Actual Result
Only the default `_id` index and the `email` unique index exist. No index is present on `verificationToken` or `resetPasswordToken`. All verification queries perform a COLLSCAN.

## Root Cause
The Mongoose schema defines both token fields without index options:

```js
// Current:
verificationToken: String,
resetPasswordToken: String,

// Fix:
verificationToken: { type: String, index: true },
resetPasswordToken: { type: String, index: true },
```

## Environment
- **File**: `backend/models/user.model.js`
- **Collection**: `users`
- **Project**: Authentication System Backend API
