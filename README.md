<div align="center">

# Authentication System — Manual & Security Testing

### *A Complete Software Testing Life Cycle (STLC) Audit on a MERN Auth Backend*

<br/>

![Test Cases](https://img.shields.io/badge/Test%20Cases-150-4A90D9?style=for-the-badge&logo=testcafe&logoColor=white)
![Passed](https://img.shields.io/badge/Passed-109-2ECC71?style=for-the-badge&logo=checkmarx&logoColor=white)
![Failed](https://img.shields.io/badge/Failed-44-E74C3C?style=for-the-badge&logo=x&logoColor=white)
![Bugs](https://img.shields.io/badge/Bugs%20Found-13-FF6B35?style=for-the-badge&logo=bugsnag&logoColor=white)

<br/>

![Pass Rate](https://img.shields.io/badge/Pass%20Rate-72.7%25-27AE60?style=flat-square)
![Critical Bugs](https://img.shields.io/badge/Critical%20Bugs-2-C0392B?style=flat-square)
![Scope](https://img.shields.io/badge/Scope-Black%20Box%20%7C%20Security%20%7C%20API-8E44AD?style=flat-square)

<br/>

**QA Engineer:** Swarup Padhy &nbsp;|&nbsp; **Test Cycle:** Initial Security & Functional Audit
**Application Under Test:** [MERN Advanced Auth](https://github.com/burakorkmez/mern-advanced-auth) by Burak Orkmez

</div>

---

## Project Overview

This repository contains all QA testing artifacts for a **MERN-stack Authentication System** backend. The objective is to demonstrate a complete **Software Testing Life Cycle (STLC)** process — including test planning, scenario design, manual test case execution across **150 cases**, and structured defect reporting with security vulnerability identification.

**Testing Scope:** Black Box Testing · Security Auditing · API Functional Testing · Static Code Analysis

---

## QA Artifacts

| # | Artifact | Description |
|---|---|---|
| 1 | [TEST_PLAN.md](./TEST_PLAN.md) | Complete test strategy — scope, entry/exit criteria, pass/fail criteria, environment, roles, and risk register (all 13 bugs) |
| 2 | [01_SCENARIOS.md](./01_SCENARIOS.md) | 113 test scenarios across 13 modules with Scenario ID, Priority, and Type classification |
| 3 | [02_TEST_CASES.md](./02_TEST_CASES.md) | 150 detailed manual test cases with Steps, Expected Result, Actual Result (code-verified), and PASS/FAIL status |
| 4 | [03_BUG_REPORTS.md](./03_BUG_REPORTS.md) | Central defect dashboard — 13 bugs tracked by Severity, Priority, and Status |
| 5 | [Bug-Reports/](./Bug-Reports/) | 13 individual bug report files — each with metadata, reproduction steps, root cause, and fix recommendation |

---

## Testing Summary

<div align="center">

| Metric | Value |
|---|---|
| 📋 Total Test Cases | **150** |
| ✅ Passed | **109** |
| ❌ Failed | **44** |
| 📈 Pass Rate | **~72.7%** |
| 🐛 Total Bugs Found | **13** |
| 🔴 Critical Bugs | **2** — BUG-01, BUG-08 |
| 🟠 Major Bugs | **7** |
| 🟡 Minor Bugs | **4** |

</div>

---

## Key Vulnerabilities Identified

| Bug ID | Severity | Vulnerability |
|---|---|---|
| `BUG-01` | 🔴 **Critical** | No rate limiting — all auth endpoints vulnerable to brute force |
| `BUG-08` | 🔴 **Critical** | NoSQL injection possible via login email field |
| `BUG-02` | 🟠 **Major** | User enumeration via forgot-password error message |
| `BUG-03` | 🟠 **Major** | Raw `error.message` leaked to client in all catch blocks |
| `BUG-06` | 🟠 **Major** | Unverified users can log in and receive a valid JWT |
| `BUG-11` | 🟠 **Major** | Race condition on concurrent single-use token endpoints |
| `BUG-13` | 🟠 **Major** | MongoDB URI with credentials logged to console at startup |

---

## Modules Tested

| Module | Test Cases | Count |
|---|---|---|
| Signup | TC-001 – TC-027 | 27 |
| Login | TC-028 – TC-044 | 17 |
| Email Verification | TC-045 – TC-055 | 11 |
| Password Recovery | TC-056 – TC-072 | 17 |
| Session & Token Management | TC-073 – TC-082 | 10 |
| Global Security & Edge Cases | TC-083 – TC-097 | 15 |
| Advanced API & Payload Validation | TC-098 – TC-107 | 10 |
| Concurrency & Performance | TC-108 – TC-114 | 7 |
| Database Integrity & Mailtrap | TC-115 – TC-122 | 8 |
| Abuse & Negative Behaviors | TC-123 – TC-137 | 15 |
| CORS & Origin Tests | TC-138 – TC-142 | 5 |
| Bcrypt & Hashing | TC-143 – TC-147 | 5 |
| UI State & UX | TC-148 – TC-150 | 3 |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Postman** | API request execution and test case validation |
| **MongoDB Compass** | Database state inspection after test execution |
| **Chrome DevTools** | Cookie and response header inspection |
| **Static Code Review** | Controller, model, middleware, and route analysis |

---

## How to Run Locally (For Verification)

### Clone the Application
```bash
git clone https://github.com/burakorkmez/mern-advanced-auth.git
cd mern-advanced-auth
```

### Configure Environment
- Rename `.env.example` to `.env`
- Add your **MongoDB URI** and **Mailtrap** credentials inside `.env`

### Start the Full Stack

```bash
# Run both frontend & backend together
npm run dev
```

```bash
# Or run separately
npm run server   # Backend  →  http://localhost:5000
npm run client   # Frontend →  http://localhost:5173
```

---

<div align="center">

### Summary

This audit demonstrates a thorough understanding of **security testing**, **defect lifecycle management**, and **manual QA processes** — identifying **2 critical vulnerabilities** and **11 additional defects** across 13 modules.

<br/>

*Tested with 🔬 Postman · 🍃 MongoDB Compass · 🛡️ Static Code Analysis*

</div>
