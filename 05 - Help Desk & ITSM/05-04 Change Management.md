---
tags: [desktop-support, itsm, change-management]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 05-04 Change Management

> [!abstract] Overview
> A guide to Change Management. This note covers change categories (Standard, Normal, Emergency), the Change Advisory Board (CAB), and rollback plan templates.

---

## 1. What Is It? (Concept Explanation)
Change Management (referred to as Change Enablement in ITIL v4) is the practice of managing updates, modifications, or retirements of IT systems to minimize risks and prevent business disruption.
*Seedha simple shabdon mein bole toh: Jab bhi company ke computers ya network mein koi bada badlav kiya jata hai (jaise anti-virus software badalna, server OS upgrade karna, ya firewalls update karna), toh use bina planning ke nahi kiya jata. Ek strict process follow hoti hai taaki updates se operations disrupt na ho.*

---

## 2. Change Classifications
ITIL categorizes changes into three main categories:

1. **Standard Changes:** Low-risk, pre-authorized changes that follow established procedures (e.g., deploying standard PC images, resetting AD passwords, or replacing broken keyboards).
2. **Normal Changes:** Medium-to-high risk changes that require formal scheduling, risk assessment, and approval by the Change Advisory Board (CAB) before implementation (e.g., migrating email to Microsoft 365, or updating business software versions).
3. **Emergency Changes:** Critical changes that must be implemented immediately to resolve a major incident (P1 outage) or patch a security vulnerability. Approved by the Emergency CAB (ECAB).

---

## 3. The Rollback Plan: A Mandatory Safety Net
Every normal change request must include a detailed **Rollback Plan**. If the change fails or causes system issues, the team must know how to quickly restore the system to its previous working state.
*Example Rollback Plan for GPO Update:*
- **Failure Trigger:** Users report they cannot open local applications after a new GPO policy is applied.
- **Rollback Steps:**
  1. Open the GPMC console.
  2. Unlink the new GPO from the target OU.
  3. Run `gpupdate /force` on a test machine to verify settings restore to default.

---
## 2. Technical Deep-Dive: Change Categories & CAB Approvals
Change Management (Change Enablement) controls modifications to IT infrastructure to minimize service disruption:
- **Standard Changes:** Low-risk, pre-authorized changes that follow standard procedures (e.g., deploying a standard PC image, updating software on client machines).
- **Normal Changes:** Moderate-to-high risk changes that require scheduling, risk assessment, and Change Advisory Board (CAB) approval (e.g., upgrading the corporate VPN gateway).
- **Emergency Changes:** Critical changes that must be implemented immediately to resolve an outage or security vulnerability, approved by an emergency CAB (e.g., patching a zero-day exploit).
### Ticket 1: Standard Change for Staging Lab Update
- **Incident ID:** CHG105401
- **Priority:** Standard
- **Problem Statement:** "Apply the June security patch package to the SCCM master deployment image."
- **Execution Plan:**
  1. Created a backup of the current boot image (`boot.wim`).
  2. Injected the patch package into the test deployment ring.
  3. Verified test laptops successfully imaged and activated.
- **Resolution:** Moved the updated image to production deployment servers, updated the CMDB record, and closed the change ticket.
### Change Management Rollback Plan Structure
- **Trigger:** If the installation fails at step 4 or if client PCs fail to PXE boot.
- **Rollback Steps:**
  1. Restore the backup boot image (`boot.old`) on the SCCM server.
  2. Restart the WDS service (`Restart-Service WDSServer`).
  3. Image a test client to verify restoration.
**Q1: What is the purpose of a Change Advisory Board (CAB)?**
A: The CAB is a committee of representatives from various IT divisions (infrastructure, security, support) that reviews, evaluates, and schedules non-standard changes. Their goal is to assess risks, identify conflicts, and ensure changes do not disrupt business operations.

## The Post-Implementation Review (PIR) Process
Following the execution of a Normal Change (e.g., upgrading the corporate VPN gateway), the Change Enablement team coordinates a Post-Implementation Review (PIR).

### Key Objectives of the PIR
- Verify if the change successfully met all defined deployment criteria and objectives.
- Audit if any user incidents or tickets were triggered by the change.
- Verify if the rollback plan was triggered during implementation.
- Confirm that the CMDB database records were updated to reflect the new configurations.

### Change Advisory Board (CAB) & Emergency Changes
- **CAB Role:** The CAB is a committee of representatives from various IT divisions (security, systems, support) that meets weekly to assess the risks, scheduling conflicts, and resource requirements of all proposed changes.
- **Emergency Changes:** When a security breach or server outage requires immediate modification, an Emergency CAB (ECAB) is convened. Approvals are obtained quickly via email or bridge call, and the change is logged retroactively to maintain compliance records.

## Additional Pro-Tips for Change Controls
During a Change Advisory Board (CAB) meeting, desktop support engineers represent the front-line perspective, detailing potential user-facing impacts. For example, when a change is scheduled to update the ERP database, support must verify that local cache databases on client machines do not need manual synchronization. Pre-approved changes (Standard Changes) must still follow strict execution checklists, and all results must be logged in the Change Request to maintain compliance standards.

## Related Notes
- [[13-02 New PC Setup & Imaging SOP]] - Baseline updates
- [[05-01 ITIL v4 Foundation for Support Engineers]] - ITIL workflows