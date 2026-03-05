# Test Plan — Authentication System
**Document ID**: TP-AUTH-01
**Author**: Swarup Padhy (QA Engineer)
**Status**: Final
**Project**: MERN Advanced Auth (by Burak Orkmez)

---

## 1. Introduction
This document defines the complete testing strategy for the Authentication System backend API. It covers the planning, scope, approach, resources, risks, and criteria governing all testing activities conducted during the Initial Security & Functional Audit cycle.

---

## 2. Objectives
- Validate all authentication flows operate correctly under normal and edge-case conditions.
- Identify and document security vulnerabilities before production deployment.
- Verify all input validation, error handling, and session management mechanisms.
- Confirm data integrity between API responses and the MongoDB user collection.

---

## 3. Scope

### 3.1 In-Scope
| Module | Features Covered |
|--------|-----------------|
| **Signup** | Valid registration, input validation, email format, password hashing, duplicate email |
| **Login** | Valid login, invalid credentials, unverified user access, JWT issuance |
| **Email Verification** | Token validation, expiry, one-time use, DB cleanup |
| **Password Recovery** | Forgot password flow, reset token lifecycle, user enumeration prevention |
| **Session Management** | `check-auth`, JWT cookie flags, tampered token handling, logout |
| **Security** | Rate limiting, NoSQL injection, XSS, CORS, brute force, information leakage |
| **API & Payload** | HTTP method restrictions, body size limits, type coercion, ReDoS |
| **Concurrency** | Race conditions on single-use tokens, simultaneous login sessions |
| **Database** | Index verification, password hashing, timestamp integrity, unique constraints |
| **Performance** | Load testing, bcrypt CPU impact, Slowloris DoS resilience |

### 3.2 Out-of-Scope
- Non-authentication features (product catalog, cart, orders, etc.)
- Third-party system outages (e.g., Mailtrap/MongoDB downtime), except for error handling behaviour
- Frontend UI visual regression testing
- Automated regression pipeline (CI/CD)

---

## 4. Test Types & Approach

| Test Type | Description | Primary Tools |
|-----------|-------------|---------------|
| **Functional** | Verify all auth flows against API specifications | Postman |
| **Negative** | Invalid inputs, edge payloads, empty bodies | Postman |
| **Security** | Brute force, injection, enumeration, JWT attacks | Postman |
| **API** | Status codes, response schema, headers, CORS | Postman, Browser DevTools |
| **Database** | Hash storage, index checks, field integrity | MongoDB Compass |
| **Performance** | Concurrent load, bcrypt stress, Slowloris | JMeter |
| **Static Analysis** | Code-level defect identification without execution | Manual code review |

---

## 5. Test Environment

| Component | Details |
|-----------|---------|
| **Runtime** | Node.js with Express.js |
| **Database** | MongoDB (local or Atlas) |
| **Frontend** | React/Vite — `http://localhost:5173` |
| **Email** | Mailtrap (sandbox SMTP) |
| **API Base URL** | `http://localhost:5000/api/auth` |
| **Tools** | Postman, MongoDB Compass |

---

## 6. Test Deliverables

| Artifact | Location |
|----------|---------|
| Test Plan | `TEST_PLAN.md` |
| Test Scenarios | `01_SCENARIOS.md` |
| Test Cases (150) | `02_TEST_CASES.md` |
| Defect Dashboard | `03_BUG_REPORTS.md` |
| Individual Bug Reports | `Bug-Reports/` |

---

## 7. Roles & Responsibilities

| Role | Name | Responsibility |
|------|------|----------------|
| QA Engineer | Swarup Padhy | Test planning, scenario design, test case execution, bug reporting |
| Developer (AUT author) | Burak Orkmez | Application Under Test source code |

---

## 8. Risk & Mitigation

| # | Risk | Severity | Linked Bug | Mitigation |
|---|------|----------|-----------|------------|
| 1 | No rate limiting on auth endpoints — brute force possible | Critical | BUG-01 | Implement `express-rate-limit`; test after fix |
| 2 | User enumeration via forgot-password error message | Major | BUG-02 | Return generic success message for all email inputs |
| 3 | Internal `error.message` exposed in catch blocks | Major | BUG-03 | Sanitize all catch blocks; log internally only |
| 4 | No password strength enforcement | Minor | BUG-04 | Add regex or `zxcvbn` validation before hashing |
| 5 | Malformed emails accepted and stored | Major | BUG-05 | Add `match` regex or `express-validator` on email |
| 6 | Unverified users can log in with full JWT | Major | BUG-06 | Add `isVerified` check in login controller |
| 7 | Invalid JWT returns HTTP 500 instead of 401 | Major | BUG-07 | Differentiate `JsonWebTokenError` in catch block |
| 8 | NoSQL injection possible via login email field | Critical | BUG-08 | Add `express-mongo-sanitize` middleware |
| 9 | No JSON 404 handler — HTML page exposed | Minor | BUG-09 | Register catch-all 404 middleware in `index.js` |
| 10 | `X-Powered-By: Express` header discloses tech stack | Minor | BUG-10 | `app.disable("x-powered-by")` or use `helmet` |
| 11 | Race condition on concurrent single-use token endpoints | Major | BUG-11 | Replace `findOne + save` with `findOneAndUpdate` |
| 12 | `verificationToken` not indexed — full collection scan | Minor | BUG-12 | Add `index: true` to `verificationToken` field |
| 13 | MongoDB URI with credentials logged to console | Major | BUG-13 | Remove `console.log(process.env.MONGO_URI)` |
