# Impact vs Effort Matrix

This document provides a prioritized breakdown of the proposed feature specifications based on a 2×2 Impact vs Effort matrix.

---

## The Matrix

| | Low Effort | High Effort |
|---|---|---|
| **High Impact** | **Quick Wins**<br>• Persistent Search Filters (2)<br>• Berth Preference Preservation (3) | **Major Projects**<br>• Tatkal Virtual Queue & Capacity Control (1)<br>• Clean SVG Captchas & Audio/OTP (5) |
| **Low Impact** | **Fill-Ins**<br>• Session Timeout Countdowns (4) | **Time Sinks**<br>• Direct Cancellation & Refund Estimates (6) |

---

## Pre-placement Scoring Table

To score each problem systematically, we evaluate Impact and Effort on a scale from 1 (lowest) to 5 (highest) using the following metrics:
- **Impact Criteria:** Volume of affected users, severity of the consequence (e.g., failed booking, trip cancellation), and presence in the core transaction path.
- **Effort Criteria:** Code scope (frontend vs. full stack), infrastructural complexity, risk of regression, and dependency on external/third-party Railway API systems.

| # | Solution Name | Affected Users (Part A) | System Components Touched | Impact Score (1-5) | Effort Score (1-5) | Quadrant Placement |
|---|---|---|---|---|---|---|
| 1 | Tatkal Virtual Queue | Millions (Daily peak hours) | Frontend + Redis Backend + API Router | 5 | 4 | **Major Project** |
| 2 | Persistent Search Filters | All search users (Date shifters) | Frontend router state only | 4 | 2 | **Quick Win** |
| 3 | Berth Preference Preservation | Families and senior citizens | Passenger form component | 4 | 2 | **Quick Win** |
| 4 | Session Timeout Countdown | Keyboard-heavy & accessibility users | Frontend timer + minor API | 3 | 2 | **Fill-In** |
| 5 | Clean SVG Captchas & OTP | All logging-in users | Auth API + SMS Gateway + Captcha engine | 5 | 4 | **Major Project** |
| 6 | Direct Cancellation & Estimates | Cancelling passengers (Peripheral) | Dashboard widget + CRIS Refund Engine | 2 | 5 | **Time Sink** |

---

## Placement Justifications

### 1. Tatkal Virtual Queue & Capacity Control — Major Project (High Impact, High Effort)
- **Impact Justification:** The Tatkal booking window affects millions of daily commuters trying to secure urgent travel, and server crashes result in failed bookings and lost money, making this a critical usability blocker (Impact: 5).
- **Effort Justification:** Implementing a virtual queue requires provisioning new high-performance Redis cache clusters, establishing Server-Sent Events/WebSocket infrastructure, and refactoring the API checkout flow to validate queue tokens (Effort: 4).
- **Prioritization Meaning:** This is a key strategic improvement that should be planned and resourced as a major sprint goal, as it resolves the platform's biggest bottleneck.

### 2. Persistent Search Filters — Quick Win (High Impact, Low Effort)
- **Impact Justification:** This affects a high volume of search users who check availability across dates, eliminating the frustration of re-entering filter choices on every click (Impact: 4).
- **Effort Justification:** This change is restricted purely to frontend client-side routing, query parameter serialization, and React/Next.js state syncing, requiring zero database or backend alterations (Effort: 2).
- **Prioritization Meaning:** This should be built in the first sprint due to its immediate user satisfaction return for minimal development cost.

### 3. Berth Preference Preservation — Quick Win (High Impact, Low Effort)
- **Impact Justification:** Reseting birth selections results in elderly and disabled passengers getting assigned upper/middle berths, creating high physical discomfort and booking dissatisfaction (Impact: 4).
- **Effort Justification:** The effort is very low as it only requires refactoring the passenger details loop in the frontend code to track rows using UUID keys instead of index numbers (Effort: 2).
- **Prioritization Meaning:** This is a zero-dependency bug fix that can be completed immediately to improve passenger assignment correctness.

### 4. Session Timeout Countdown Alert — Fill-In (Low Impact, Low Effort)
- **Impact Justification:** While session timeouts are extremely frustrating, they primarily affect slow typers or users entering large numbers of passengers, making it a lower-volume issue compared to search or login (Impact: 3).
- **Effort Justification:** The implementation only requires a local JavaScript timer hook on the frontend and a minor session refresh endpoint that updates the cookie lifetime (Effort: 2).
- **Prioritization Meaning:** This is a minor improvement that can be scheduled when team capacity allows or paired with other authentication refactors.

### 5. Clean SVG Captchas & OTP Authentication — Major Project (High Impact, High Effort)
- **Impact Justification:** Captchas block 100% of logging-in and paying users, and visual/audio failures lock out visually impaired passengers and cause failed bookings during Tatkal spikes (Impact: 5).
- **Effort Justification:** Implementing secure SVG generation, fixing the server-side audio captcha generator to match accessibility standards, and integrating a third-party SMS/OTP gateway API is a full-stack effort (Effort: 4).
- **Prioritization Meaning:** Essential project for regulatory and accessibility compliance, which must be systematically scheduled and thoroughly tested for security loopholes.

### 6. Direct Dashboard Cancellation & Refund Estimates — Time Sink (Low Impact, High Effort)
- **Impact Justification:** Ticket cancellation is a peripheral feature used by a small percentage of passengers, meaning improvements do not impact the core booking or conversion metrics (Impact: 2).
- **Effort Justification:** Refunding is controlled by complex railway policies and CRIS backend systems, requiring deep API handshakes with old ticketing mainframes and real-time transaction reconciliation (Effort: 5).
- **Prioritization Meaning:** This should be deprioritized or deferred to long-term roadmaps, as the cost of integration is disproportionately high for the value it delivers.

---

## Recommended Sprint Order

1. **Berth Preference State Preservation (3):** Immediate fix for an accessibility-adjacent bug with no external API dependencies.
2. **Persistent Search Filters (2):** Quick front-end win that significantly improves search usability.
3. **Session Timeout Countdown Alert (4):** Simple timer integration to prevent silent data loss.
4. **Tatkal Virtual Queue & Capacity Control (1):** High-priority project to solve peak load crashes before peak seasons.
5. **Clean SVG Captchas & OTP Authentication (5):** Security and accessibility overhaul to streamline user entry.
6. **Direct Dashboard Cancellation & Refund Estimates (6):** Deferred to future quarters due to CRIS mainframe integration costs.
