# [BUG-13] MongoDB Connection String with Credentials Logged to Console

## Issue Summary
| Field | Detail |
|-------|--------|
| **ID** | BUG-13 |
| **Title** | MongoDB Connection String with Credentials Logged to Console |
| **Severity** | Major |
| **Priority** | P2 |
| **Status** | Open |
| **Reporter** | Swarup Padhy |
| **Date Reported** | 2026-03-05 |
| **Affected Version** | 1.0.0 (Initial Release) |
| **Related Test Cases** | TC-135 |

## Description
On every server start, `connectDB.js` explicitly logs the full `MONGO_URI` environment variable to the console via `console.log("mongo_uri: ", process.env.MONGO_URI)`. MongoDB connection strings typically embed the username, password, host, and database name in plaintext (e.g., `mongodb+srv://admin:Secret123@cluster.mongodb.net/mydb`). This exposes sensitive credentials in server logs, CI/CD pipeline output, log aggregation tools, and any environment where console output is collected.

## Steps to Reproduce
1. Start the backend server with `npm run dev` or `node index.js`.
2. Observe the terminal output immediately after startup.

## Expected Result
The server should log only a safe confirmation message such as `"MongoDB Connected: <host>"` without including the connection string or credentials.

## Actual Result
The full `MONGO_URI` value — including embedded username, password, and host — is printed to the console:
```
mongo_uri:  mongodb+srv://admin:Secret123@cluster0.mongodb.net/authdb
MongoDB Connected: cluster0.mongodb.net
```

## Root Cause
An unnecessary `console.log` debug statement in `connectDB.js` exposes the environment variable:

```js
// Line 5 in connectDB.js — must be removed:
console.log("mongo_uri: ", process.env.MONGO_URI);
```

## Recommended Fix
Remove the `console.log` statement entirely. The existing `"MongoDB Connected: ${conn.connection.host}"` log on line 7 is sufficient and safe.

## Environment
- **File**: `backend/db/connectDB.js` (line 5)
- **Trigger**: Every server startup
- **Project**: Authentication System Backend API
