# Authentication System — Manual & Security Testing

**QA Engineer**: Swarup Padhy
**Test Cycle**: Initial Security & Functional Audit
**Application Under Test**: [MERN Advanced Auth](https://github.com/burakorkmez/mern-advanced-auth) by Burak Orkmez

---

## Project Overview
This repository contains all QA testing artifacts for a MERN-stack Authentication System backend. The objective is to demonstrate a complete Software Testing Life Cycle (STLC) process, including test planning, scenario design, manual test case execution (150 cases), and structured defect reporting with security vulnerability identification.

**Testing Scope**: Black Box Testing, Security Auditing, API Functional Testing, Static Code Analysis.

---

## QA Artifacts

| # | File / Folder | Description |
|---|--------------|-------------|
| 1 | [TEST_PLAN.md](./TEST_PLAN.md) | Complete test strategy — scope, entry/exit criteria, pass/fail criteria, environment, roles, and risk register (all 13 bugs) |
| 2 | [01_SCENARIOS.md](./01_SCENARIOS.md) | 113 test scenarios across 13 modules with Scenario ID, Priority, and Type classification |
| 3 | [02_TEST_CASES.md](./02_TEST_CASES.md) | 150 detailed manual test cases with Steps, Expected Result, Actual Result (code-verified), and PASS/FAIL status |
| 4 | [03_BUG_REPORTS.md](./03_BUG_REPORTS.md) | Central defect dashboard — 13 bugs tracked by Severity, Priority, and Status |
| 5 | [Bug-Reports/](./Bug-Reports/) | 13 individual bug report files — each with metadata, reproduction steps, root cause, and fix recommendation |

---

## Testing Summary

| Metric | Value |
|--------|-------|
| Total Test Cases | 150 |
| Passed | 109 |
| Failed | 44 |
| Pass Rate | ~72.7% |
| Total Bugs Found | 13 |
| Critical Bugs | 2 (BUG-01, BUG-08) |
| Major Bugs | 7 |
| Minor Bugs | 4 |

### Key Vulnerabilities Identified
| ID | Severity | Vulnerability |
|----|----------|--------------|
| BUG-01 | Critical | No rate limiting — all auth endpoints vulnerable to brute force |
| BUG-08 | Critical | NoSQL injection possible via login email field |
| BUG-02 | Major | User enumeration via forgot-password error message |
| BUG-03 | Major | Raw `error.message` leaked to client in all catch blocks |
| BUG-06 | Major | Unverified users can log in and receive a valid JWT |
| BUG-11 | Major | Race condition on concurrent single-use token endpoints |
| BUG-13 | Major | MongoDB URI with credentials logged to console at startup |

---

## Modules Tested

| Module | Test Cases |
|--------|-----------|
| Signup | TC-001 – TC-027 |
| Login | TC-028 – TC-044 |
| Email Verification | TC-045 – TC-055 |
| Password Recovery | TC-056 – TC-072 |
| Session & Token Management | TC-073 – TC-082 |
| Global Security & Edge Cases | TC-083 – TC-097 |
| Advanced API & Payload Validation | TC-098 – TC-107 |
| Concurrency & Performance | TC-108 – TC-114 |
| Database Integrity & Mailtrap | TC-115 – TC-122 |
| Abuse & Negative Behaviors | TC-123 – TC-137 |
| CORS & Origin Tests | TC-138 – TC-142 |
| Bcrypt & Hashing | TC-143 – TC-147 |
| UI State & UX | TC-148 – TC-150 |

---

## How to Run Locally (For Verification)

1. **Clone the application**:
   ```bash
   git clone https://github.com/burakorkmez/mern-advanced-auth.git
   cd mern-advanced-auth
   ```

2. **Configure environment**:
   - Copy `.env.example` to `.env`
   - Add your MongoDB URI and Mailtrap credentials

3. **Start the full stack**:
   ```bash
   npm run dev
   ```
   Or separately:
   ```bash
   npm run server   # Backend on port 5000
   npm run client   # Frontend on port 5173
   ```

4. **Import Postman collection** and set base URL to `http://localhost:5000/api/auth`

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Postman | API request execution and test case validation |
| MongoDB Compass | Database state inspection after test execution |
| Chrome DevTools | Cookie and response header inspection |
| OWASP ZAP | Automated vulnerability scanning (referenced) |
| JMeter / K6 | Performance and concurrent load testing (referenced) |
| Static Code Review | Controller, model, middleware, and route analysis |
