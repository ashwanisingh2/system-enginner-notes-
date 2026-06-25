---
tags: [desktop-support, itsm, sla]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 05-09 SLA Management & Reporting

> [!abstract] Overview
> A guide to Service Level Agreements (SLAs) in desktop support. This note covers response and resolution SLAs, Operational Level Agreements, and tracking metrics.

---

## 1. What Is It? (Concept Explanation)
A **Service Level Agreement (SLA)** is a formal agreement between an IT service provider and its customers, defining the expected level of service, including response and resolution times.
*Seedha simple shabdon mein bole toh: SLA ek agreement hai jo batata hai ki IT team kis category ke ticket ko kitni der mein handle karegi. Jaise agar koi high priority P1 server issue hai, toh use resolve karne ke liye 4 ghante (SLA) milte hain. Agar task time par poora nahi hota, toh use SLA Breach bolte hain.*

---

## 2. SLAs, OLAs, and UCs

- **Service Level Agreement (SLA):** Commitments between the IT department and the business users (e.g., "P2 incidents will be resolved within 8 business hours").
- **Operational Level Agreement (OLA):** Internal agreements between different IT teams (e.g., "The network team will assign IP configurations within 2 hours of a desktop team request").
- **Underpinning Contract (UC):** Agreements between the IT department and external vendors (e.g., "Dell will replace laptop screens within next business day").

---

## 3. Incident Priority and SLA Matrix
Most corporate organizations use a grid to track commitments based on ticket priority:

| Ticket Priority | Description | Target Response Time | Target Resolution Time |
|---|---|---|---|
| **P1 - Critical** | Whole site or VIP user down | < 15 Minutes | < 4 Hours |
| **P2 - High** | Department offline or key app failing | < 1 Hour | < 8 Hours (1 Business Day) |
| **P3 - Medium** | Single user system error / Workaround exists | < 4 Hours | < 24 Hours (3 Business Days) |
| **P4 - Low** | Minor query / Keyboard swap | < 12 Hours | < 5 Business Days |

---
## 2. Technical Deep-Dive: SLA Calculations & Pending States
Service Level Agreements (SLAs) define the target metrics for IT delivery:
- **Response SLA:** The time from ticket creation until a support engineer assigns the ticket and changes its status to "In Progress".
- **Resolution SLA:** The total time from ticket creation until the ticket is set to "Resolved".
- **Pending States (SLA Pausing):** If a technician requires input from a user or parts from a vendor, they change the ticket status to **Pending Customer** or **Pending Vendor**. This pauses the SLA clock, preventing SLA breaches caused by external delays.
### Ticket 1: SLA Breach due to Missing Pending Status
- **Incident ID:** INC105921
- **Priority:** P2 (Critical Printer Offline)
- **Problem Statement:** "The ticket was unresolved for 4 days, triggering an SLA breach notification to management."
- **Diagnostics:**
  1. Checked the ticket history. The technician had diagnosed a failed formatter board on Day 1.
  2. They ordered the part from HP and waited 3 days for delivery.
  3. However, they left the ticket status as "In Progress", meaning the resolution SLA clock kept running.
- **Resolution:** Set the ticket status to "Pending Vendor" retroactively. Instructed the support team to use pending states to reflect active delays and maintain accurate SLA reporting.
### Standard Corporate SLA Target Table

| Ticket Priority | Response SLA Target | Resolution SLA Target | SLA Clock Rule |
|---|---|---|---|
| **P1 - Critical** | < 15 Minutes | < 4 Hours | 24/7 Running |
| **P2 - High** | < 30 Minutes | < 8 Hours | 24/7 Running |
| **P3 - Moderate** | < 2 Hours | < 3 Business Days | Business Hours Only |
| **P4 - Low** | < 4 Hours | < 5 Business Days | Business Hours Only |
**Q1: What is the purpose of placing a ticket in "Pending Customer" status?**
A: Placing a ticket in "Pending Customer" status pauses the active SLA resolution clock. This ensures the IT support team is not penalized for delays caused by users who are away or slow to provide required details.

## SLA vs OLA (Operational Level Agreement)
To deliver service levels successfully, support teams coordinate SLAs with internal agreements:

- **SLA (Service Level Agreement):** The agreement between the IT department and the business (e.g., resolving P2 incidents within 8 hours).
- **OLA (Operational Level Agreement):** The agreement between internal IT teams detailing how they will coordinate. For example, if the Help Desk has an 8-hour SLA, they might establish a 2-hour OLA with the Network team to resolve port blocks, ensuring target timelines are met.
- **UC (Underpinning Contract):** The agreement with external vendors (e.g. Dell hardware support).

### Key Support Metrics (KPIs)
- **MTTR (Mean Time to Repair):** The average time taken to resolve an incident from creation to closure.
- **FCR (First Contact Resolution):** The percentage of tickets resolved during the user's initial contact.
- **AHT (Average Handling Time):** The active time a technician spends troubleshooting a ticket.

## Related Notes
- [[05-02 Incident Management]] - Resolving incidents
- [[13-09 Shift Handover Checklist]] - Backlog monitoring