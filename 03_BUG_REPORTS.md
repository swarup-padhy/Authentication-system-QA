# Defect Dashboard

**Project**: Authentication System
**Test Cycle**: Initial Security & Functional Audit

## Summary Statistics
| Total Bugs | Critical | Major | Minor | Status |
|:----------:|:--------:|:-----:|:-----:|:------:|
| 13         | 2        | 7     | 4     | **Open** |

## Defect List

| ID | Severity | Title | Status | Link |
|----|----------|-------|--------|------|
| **BUG-01** | Critical | **Missing Rate Limiting Allows Brute Force on Auth Endpoints** | Open | [View Report](./Bug-Reports/BUG-01_Missing_Rate_Limiting.md) |
| **BUG-02** | Major | **User Enumeration Vulnerability in Forgot Password** | Open | [View Report](./Bug-Reports/BUG-02_User_Enumeration_Forgot_Password.md) |
| **BUG-03** | Major | **Information Leak via Unhandled Exception error.message** | Open | [View Report](./Bug-Reports/BUG-03_Information_Leak_Error_Message.md) |
| **BUG-04** | Minor | **No Password Strength Validation on Signup or Password Reset** | Open | [View Report](./Bug-Reports/BUG-04_No_Password_Strength_Validation.md) |
| **BUG-05** | Major | **No Email Format Validation on Signup** | Open | [View Report](./Bug-Reports/BUG-05_No_Email_Format_Validation.md) |
| **BUG-06** | Major | **Unverified Users Can Log In Without Email Verification** | Open | [View Report](./Bug-Reports/BUG-06_Unverified_User_Can_Login.md) |
| **BUG-07** | Major | **Tampered or Invalid JWT Returns HTTP 500 Instead of HTTP 401** | Open | [View Report](./Bug-Reports/BUG-07_Invalid_JWT_Returns_500_Not_401.md) |
| **BUG-08** | Critical | **NoSQL Injection Vulnerability on Login Endpoint** | Open | [View Report](./Bug-Reports/BUG-08_NoSQL_Injection_Login.md) |
| **BUG-09** | Minor | **No Custom 404 Handler — Express Returns HTML Error Page** | Open | [View Report](./Bug-Reports/BUG-09_No_Custom_404_Handler.md) |
| **BUG-10** | Minor | **X-Powered-By: Express Header Exposed in All Responses** | Open | [View Report](./Bug-Reports/BUG-10_X_Powered_By_Header_Exposed.md) |
| **BUG-11** | Major | **Race Condition Allows Concurrent Requests to Consume Single-Use Tokens** | Open | [View Report](./Bug-Reports/BUG-11_Race_Condition_Concurrent_Requests.md) |
| **BUG-12** | Minor | **verificationToken Field Has No Database Index** | Open | [View Report](./Bug-Reports/BUG-12_Verification_Token_Not_Indexed.md) |
| **BUG-13** | Major | **MongoDB Connection String with Credentials Logged to Console** | Open | [View Report](./Bug-Reports/BUG-13_MongoDB_URI_Logged_To_Console.md) |

---
*Note: This dashboard tracks issues found during the manual and static analysis testing phase.*
