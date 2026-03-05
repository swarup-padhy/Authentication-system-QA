# 01 - Test Scenarios

**Document ID**: TS-AUTH-01
**Version**: 1.0
**Date**: 2026-03-05
**Author**: Swarup Padhy (QA Engineer)

---

> **Legend**
> **Priority**: P1 = Critical Path | P2 = High | P3 = Medium | P4 = Low
> **Type**: Functional (F) | Security (S) | Negative (N) | Boundary (B) | Performance (P) | Database (DB) | UI (UI)

---

## 1. Signup Module

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-SIGNUP-01-01 | Successful signup with standard valid inputs | P1 | F |
| TS-SIGNUP-01-02 | Signup with maximum character length for Name (255 chars) | P3 | B |
| TS-SIGNUP-01-03 | Signup with minimum character length for Name (1–2 chars) | P3 | B |
| TS-SIGNUP-01-04 | Signup with Unicode/international characters in Name | P3 | F |
| TS-SIGNUP-01-05 | Signup with a very long valid email address | P3 | B |
| TS-SIGNUP-01-06 | Verify password is bcrypt-hashed before DB storage | P1 | S |
| TS-SIGNUP-01-07 | Verify JWT token is generated and set as cookie on signup | P1 | F |
| TS-SIGNUP-01-08 | Verify auth cookie is created with correct security flags | P1 | S |
| TS-SIGNUP-01-09 | Verify verification token is generated with 24-hour expiry | P1 | F |
| TS-SIGNUP-02-01 | Signup fails when Name field is empty | P1 | N |
| TS-SIGNUP-02-02 | Signup fails when Email field is empty | P1 | N |
| TS-SIGNUP-02-03 | Signup fails when Password field is empty | P1 | N |
| TS-SIGNUP-02-04 | Signup fails with completely empty JSON body `{}` | P1 | N |
| TS-SIGNUP-02-05 | Signup fails with email missing `@` symbol | P2 | N |
| TS-SIGNUP-02-06 | Signup fails with invalid email domain format | P2 | N |
| TS-SIGNUP-02-07 | Signup fails with multiple `@` symbols in email | P2 | N |
| TS-SIGNUP-02-08 | Signup email with leading/trailing whitespace is trimmed or rejected | P2 | N |
| TS-SIGNUP-02-09 | Signup fails with password below minimum length (< 6 chars) | P2 | N |
| TS-SIGNUP-02-10 | Signup fails with purely numeric password | P2 | N |
| TS-SIGNUP-02-11 | Signup fails with dictionary-word only password | P2 | N |
| TS-SIGNUP-02-12 | Signup fails for duplicate email (exact case match) | P1 | N |
| TS-SIGNUP-02-13 | Signup with duplicate email in different case (case-insensitive check) | P2 | N |
| TS-SIGNUP-03-01 | Signup rejects 10MB body payload (413 Payload Too Large) | P2 | B |
| TS-SIGNUP-03-02 | XSS payload in Name field is stored/sanitized safely | P2 | S |
| TS-SIGNUP-03-03 | SQL/NoSQL injection payload in Email field is rejected | P2 | S |
| TS-SIGNUP-03-04 | Signup fails gracefully when MongoDB is unavailable | P2 | N |
| TS-SIGNUP-03-05 | Signup behaviour when Mailtrap email service fails | P2 | N |

---

## 2. Login Module

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-LOGIN-01-01 | Successful login with valid registered credentials | P1 | F |
| TS-LOGIN-01-02 | Login updates `lastLogin` timestamp in DB | P2 | F |
| TS-LOGIN-01-03 | Login generates a new valid JWT token | P1 | F |
| TS-LOGIN-01-04 | Login sets `token` cookie overriding previous session | P1 | F |
| TS-LOGIN-01-05 | Unverified users can/cannot log in (isVerified check) | P2 | S |
| TS-LOGIN-02-01 | Login fails with a non-existent email address | P1 | N |
| TS-LOGIN-02-02 | Login fails with correct email and wrong password | P1 | N |
| TS-LOGIN-02-03 | Login fails with correct email and empty password | P1 | N |
| TS-LOGIN-02-04 | Login fails with empty email field | P1 | N |
| TS-LOGIN-02-05 | Login fails with completely empty JSON body `{}` | P1 | N |
| TS-LOGIN-02-06 | NoSQL injection attempt via login email field `{"$gt":""}` | P1 | S |
| TS-LOGIN-02-07 | Number type (instead of String) passed as password field | P3 | B |
| TS-LOGIN-02-08 | Login with trailing whitespace on registered email | P3 | N |
| TS-LOGIN-03-01 | Rate limiting triggers after 5 consecutive failed logins | P1 | S |
| TS-LOGIN-03-02 | Rate limiting triggers after 50 consecutive failed logins | P1 | S |
| TS-LOGIN-03-03 | Concurrent login from 3 simultaneous browser tabs | P3 | P |
| TS-LOGIN-03-04 | Login fails gracefully when MongoDB is unavailable | P2 | N |

---

## 3. Email Verification Module

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-VERIFY-01-01 | Email verified successfully with valid active code | P1 | F |
| TS-VERIFY-01-02 | `isVerified` flag is set to `true` in DB after verification | P1 | DB |
| TS-VERIFY-01-03 | `verificationToken` and expiry are cleared from DB after verification | P1 | DB |
| TS-VERIFY-01-04 | Welcome email is dispatched on successful verification | P2 | F |
| TS-VERIFY-02-01 | Verification fails with empty code submitted `{}` | P1 | N |
| TS-VERIFY-02-02 | Verification fails with incorrect format (non-digit string) | P2 | N |
| TS-VERIFY-02-03 | Verification fails with a random wrong 6-digit code | P1 | N |
| TS-VERIFY-02-04 | Verification fails when token is expired (> 24 hours) | P1 | N |
| TS-VERIFY-02-05 | Verification when Mailtrap/welcome email service fails | P3 | N |
| TS-VERIFY-02-06 | Integer type passed as code `{"code": 123456}` handles without crash | P3 | B |
| TS-VERIFY-02-07 | Re-verification of already verified account fails cleanly | P2 | N |

---

## 4. Password Recovery Module

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-RECOVERY-01-01 | Forgot password initiates successfully for valid email | P1 | F |
| TS-RECOVERY-01-02 | Reset token is generated and stored in DB | P1 | DB |
| TS-RECOVERY-01-03 | `resetPasswordExpiresAt` is set to current time + 1 hour | P1 | DB |
| TS-RECOVERY-01-04 | Password reset email dispatched with correct link via Mailtrap | P2 | F |
| TS-RECOVERY-01-05 | Unregistered email returns generic response (no user enumeration) | P1 | S |
| TS-RECOVERY-01-06 | Forgot password fails if email field is empty | P1 | N |
| TS-RECOVERY-01-07 | Forgot password fails with invalid email format | P2 | N |
| TS-RECOVERY-02-01 | Password reset succeeds with valid token and strong password | P1 | F |
| TS-RECOVERY-02-02 | Successful reset sends confirmation email | P2 | F |
| TS-RECOVERY-02-03 | `resetPasswordToken` and `resetPasswordExpiresAt` are cleared after reset | P1 | DB |
| TS-RECOVERY-02-04 | Old password no longer works after successful reset | P1 | F |
| TS-RECOVERY-02-05 | New password works for login after successful reset | P1 | F |
| TS-RECOVERY-02-06 | Reset fails with empty/missing password in body | P1 | N |
| TS-RECOVERY-02-07 | Reset fails with weak password (BUG-004) | P2 | N |
| TS-RECOVERY-02-08 | Reset fails with invalid/tampered token in URL | P1 | S |
| TS-RECOVERY-02-09 | Reset fails with expired token | P1 | N |
| TS-RECOVERY-02-10 | Reset with token exceeding 200 characters handles safely | P3 | B |

---

## 5. Session & Token Management

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-SESSION-01-01 | `check-auth` returns 200 with user object for valid cookie | P1 | F |
| TS-SESSION-01-02 | `check-auth` response strictly excludes `password` field | P1 | S |
| TS-SESSION-01-03 | `check-auth` returns 401 when no cookie is present | P1 | S |
| TS-SESSION-01-04 | `check-auth` returns 401 when cookie is tampered | P1 | S |
| TS-SESSION-01-05 | `check-auth` returns 400 when token is valid but user deleted | P2 | N |
| TS-SESSION-01-06 | `alg: none` JWT attack is rejected | P1 | S |
| TS-SESSION-01-07 | Cookie flags: `HttpOnly`, `SameSite=strict`, `Secure` (prod), `maxAge=7d` | P1 | S |
| TS-SESSION-02-01 | Logout clears the auth cookie and returns 200 | P1 | F |
| TS-SESSION-02-02 | Logout response contains `Set-Cookie: token=; Max-Age=0` | P1 | F |
| TS-SESSION-02-03 | Logout without active session returns 200 gracefully | P2 | N |

---

## 6. Global Security & Edge Cases

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-SEC-01-01 | Unsupported HTTP method `TRACE` returns 404 | P2 | S |
| TS-SEC-01-02 | CORS preflight from disallowed origin is rejected | P1 | S |
| TS-SEC-01-03 | Cross-origin POST from disallowed origin is rejected | P1 | S |
| TS-SEC-01-04 | Request without `Content-Type: application/json` fails cleanly | P2 | N |
| TS-SEC-01-05 | `X-Powered-By: Express` header is absent from responses | P3 | S |
| TS-SEC-01-06 | OWASP ZAP scan reports no Critical/High vulnerabilities | P1 | S |
| TS-SEC-02-01 | Unknown routes return JSON 404, not HTML error page | P2 | N |
| TS-SEC-02-02 | Malformed JSON body handled gracefully (no stack trace) | P2 | S |
| TS-SEC-03-01 | Null byte injection in email field is filtered or rejected | P2 | S |
| TS-SEC-03-02 | Duplicate JSON key parameter pollution handled safely | P3 | B |
| TS-SEC-03-03 | Brute-force of `verify-email` endpoint triggers rate limit | P1 | S |
| TS-SEC-03-04 | Very large Name payload (~100k chars) handled within limits | P2 | B |
| TS-SEC-03-05 | Internal domain email `admin@localhost` accepted/rejected per policy | P3 | N |
| TS-SEC-03-06 | `isVerified: true` in signup body does not override schema (mass assignment) | P1 | S |
| TS-SEC-03-07 | `resetPasswordToken` in signup body is strictly ignored | P1 | S |

---

## 7. Advanced API & Payload Validation

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-API-01-01 | Signup with `Content-Type: text/plain` fails gracefully | P2 | N |
| TS-API-01-02 | Signup with `Content-Type: application/xml` fails gracefully | P2 | N |
| TS-API-01-03 | Extra fields in payload (e.g., `isAdmin`) are ignored | P1 | S |
| TS-API-01-04 | Boolean passed as `name` field does not crash server | P3 | B |
| TS-API-01-05 | Array passed as `email` field does not crash server | P3 | B |
| TS-API-01-06 | Nested object passed as `name` does not crash server | P3 | B |
| TS-API-01-07 | Login with missing `password` key returns clean error | P2 | N |
| TS-API-01-08 | 5MB URL query string triggers 414/431 response | P3 | B |
| TS-API-01-09 | 10,000-char email on forgot-password — no ReDoS | P2 | S |
| TS-API-01-10 | Malicious RegExp string in email field — no ReDoS | P2 | S |

---

## 8. Concurrency & Performance

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-PERF-01-01 | Two identical concurrent signups — only one succeeds | P2 | P |
| TS-PERF-01-02 | Two identical concurrent verify-email requests — only one succeeds | P2 | P |
| TS-PERF-01-03 | Five identical concurrent reset-password requests — only one succeeds | P2 | P |
| TS-PERF-02-01 | 1,000 concurrent `check-auth` requests — server stable | P3 | P |
| TS-PERF-02-02 | 500 concurrent login requests — CPU spike, server survives | P3 | P |
| TS-PERF-02-03 | 100 concurrent invalid requests — handled and closed cleanly | P3 | P |
| TS-PERF-02-04 | Slowloris attack (500 slow connections) — server drops them | P2 | S |

---

## 9. Database Integrity & Mailtrap Integration

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-DB-01-01 | Login with `password: null` in DB handles without crash | P2 | DB |
| TS-DB-01-02 | Verification with deleted user fails cleanly | P2 | DB |
| TS-DB-01-03 | 50 rapid forgot-password requests — rate limiting or all processed | P2 | P |
| TS-DB-01-04 | Invalid receiver email format from backend — Mailtrap rejects safely | P3 | N |
| TS-DB-01-05 | `createdAt` and `updatedAt` timestamps set correctly on signup | P2 | DB |
| TS-DB-01-06 | `updatedAt` timestamp updates correctly on password reset | P2 | DB |
| TS-DB-01-07 | `verificationToken` field is indexed in MongoDB | P3 | DB |
| TS-DB-01-08 | `email` unique index prevents duplicate insertion at DB level | P1 | DB |

---

## 10. Abuse & Negative Behaviors

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-ABUSE-01-01 | 1,000,000-char password on signup handled safely (413) | P2 | B |
| TS-ABUSE-01-02 | Extremely long password on login handled gracefully | P2 | B |
| TS-ABUSE-01-03 | JWT with algorithm changed to `RS256` is rejected | P1 | S |
| TS-ABUSE-01-04 | JWT with missing signature segment is rejected | P1 | S |
| TS-ABUSE-01-05 | Arbitrary huge JWT claims do not affect DB data retrieval | P2 | S |
| TS-ABUSE-01-06 | `GET /verify-email` (wrong method) returns 404 | P2 | N |
| TS-ABUSE-01-07 | Unicode homoglyph email stored as distinct user | P3 | S |
| TS-ABUSE-01-08 | Re-submitting logout is idempotent (200 OK) | P3 | N |
| TS-ABUSE-01-09 | Invalid MongoDB URI on startup — logged and exits cleanly | P2 | N |
| TS-ABUSE-01-10 | Triggering forgot-password 100 times invalidates previous tokens | P2 | S |
| TS-ABUSE-01-11 | Repeated verify-email attempts before click — no resend endpoint | P3 | N |
| TS-ABUSE-01-12 | Passwords are never logged to server console | P1 | S |
| TS-ABUSE-01-13 | Tokens are never logged to server console | P1 | S |
| TS-ABUSE-01-14 | Timezone differences don't affect token expiry calculations | P3 | N |
| TS-ABUSE-01-15 | Different user-agent strings (mobile vs desktop) do not affect login | P4 | N |

---

## 11. CORS & Origin Tests

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-CORS-01-01 | Login from allowed origin `http://localhost:5173` succeeds | P1 | S |
| TS-CORS-01-02 | Login from different port `http://localhost:3000` is rejected by CORS | P1 | S |
| TS-CORS-01-03 | Login from external production domain is rejected by CORS | P1 | S |
| TS-CORS-01-04 | Preflight `OPTIONS` request returns 204 with correct headers | P2 | S |
| TS-CORS-01-05 | `Access-Control-Allow-Credentials: true` present in responses | P1 | S |

---

## 12. Bcrypt & Hashing

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-HASH-01-01 | Password of exactly 72 chars registers and logs in successfully | P3 | B |
| TS-HASH-01-02 | Password of 73 chars — bcrypt truncates silently at 72 bytes | P3 | B |
| TS-HASH-01-03 | Login with 73-char password matches hash of first 72 chars | P3 | B |
| TS-HASH-01-04 | Password containing null bytes `\x00` — handled without null truncation | P2 | S |
| TS-HASH-01-05 | Two users with identical passwords produce different hashes (salting) | P1 | S |

---

## 13. UI State & User Experience

| Scenario ID | Description | Priority | Type |
|-------------|-------------|----------|------|
| TS-UI-01-01 | Signup button shows loading state during API call | P2 | UI |
| TS-UI-01-02 | Tab key navigates form fields in logical order | P3 | UI |
| TS-UI-01-03 | Enter key inside password field submits the form | P3 | UI |
