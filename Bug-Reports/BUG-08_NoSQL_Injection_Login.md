# [BUG-08] NoSQL Injection Vulnerability on Login Endpoint

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-08 |
| **Title** | NoSQL Injection Vulnerability on Login Endpoint |
| **Severity** | Critical |
| **Priority** | P1 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-038 |

## Description
The `POST /api/auth/login` endpoint passes the `email` field from `req.body` directly into `User.findOne({ email })` without sanitizing or validating that it is a plain string. If an attacker sends a JSON object containing a MongoDB query operator (e.g., `{ "$gt": "" }`), Mongoose may pass it to the MongoDB driver as a query operator, potentially matching any user in the database and bypassing authentication.

## Steps to Reproduce
1. Send a `POST` request to `/api/auth/login` with the following JSON payload:
   ```json
   {
     "email": { "$gt": "" },
     "password": { "$gt": "" }
   }
   ```
2. Observe whether the server returns a successful login response.

## Expected Result
The server should return `400 Bad Request` with `"Invalid credentials"`. MongoDB operator objects must never be accepted as field values.

## Actual Result
Depending on the installed Mongoose version: `User.findOne({ email: { $gt: "" } })` may match the first user in the collection. If it does, the attacker receives `200 OK` with a valid JWT cookie without knowing any valid credentials.

## Root Cause
The `login` controller directly destructures `email` from `req.body` without validating its type:

```js
const { email, password } = req.body;
const user = await User.findOne({ email }); // email can be an object
```

No `express-mongo-sanitize` middleware or type-check (`typeof email !== "string"`) is applied.

## Recommended Fix
1. Add `express-mongo-sanitize` middleware globally in `index.js`.
2. OR add an explicit type check in the controller:
   ```js
   if (typeof email !== "string" || typeof password !== "string") {
     return res.status(400).json({ message: "Invalid credentials" });
   }
   ```

## Environment
- **Endpoint**: `POST /api/auth/login`
- **File**: `backend/controllers/auth.controller.js` (line 95)
- **Project**: Authentication System Backend API
