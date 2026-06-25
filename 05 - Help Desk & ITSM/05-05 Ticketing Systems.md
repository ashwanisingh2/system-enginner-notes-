---
tags: [desktop-support, itsm, ticketing]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 05-05 Ticketing Systems

> [!abstract] Overview
> A guide to enterprise ticketing systems (ServiceNow, Jira Service Management, Zendesk). This note covers standard ticket fields, incident logging rules, and lifecycle states.

---

## 1. What Is It? (Concept Explanation)
Ticketing systems are the database tools used by IT support teams to log, track, assign, and resolve support requests.
*Seedha simple shabdon mein bole toh: Ticketing system helpdesk ka digital register hai. Har call, email, ya walk-in request ko ek unique ticket number (jaise INC109283) milta hai. Isse support engineers ki performance track hoti hai, SLAs monitor hote hain, aur koi bhi ticket bina resolution ke miss nahi hota.*

---

## 2. Core Ticket Fields
A standard ticket includes the following key information:

- **Caller / Requestor:** The name of the user experiencing the issue.
- **Category / Subcategory:** Used for routing (e.g., *Hardware > Monitor > Flicker*).
- **Configuration Item (CI):** The specific asset affected (e.g., laptop serial number, printer hostname).
- **Short Description:** A summary of the issue (e.g., "Cannot connect to VPN").
- **Description:** A detailed description of the error message and troubleshooting steps taken.
- **Assignment Group:** The team assigned to resolve the issue (e.g., *Desktop Support Tier 1*).
- **State:** The ticket's current status (e.g., *New, In Progress, On Hold, Resolved, Closed*).

---

## 3. Real-World Scenarios

### Scenario 1: Managing Ticket Status and User Communication
- **Incident Description:** You receive a ticket regarding a laptop display replacement. You need to order parts, which will take 3 days.
- **Troubleshooting Steps:**
  1. If you leave the ticket in **In Progress** state, the SLA timer continues to run, which can lead to an SLA breach.
  2. Change the ticket state to **On Hold** (or *Pending*).
  3. Select the correct sub-state: **Pending Vendor** or **Pending Parts**. This pauses the active SLA timer.
- **Resolution:**
  - Add a work note explaining the delay: "Ordered LCD screen panel from Dell. Expected delivery on 2026-06-28."
  - Once the parts arrive and you begin the repair, set the ticket state back to **In Progress**.

---
## 2. Technical Deep-Dive: API Integrations & Ticket Lifecycle
Ticketing systems (e.g., ServiceNow, Jira Service Desk) act as the system of record for IT operations.
- **Workflow Automation:** When a user submits an onboarding form, the system automatically runs workflows: creating AD accounts, generating task tickets for hardware imaging, and sending notification emails.
- **CMDB Integration:** Each ticket can be linked to a Configuration Item (CI) (e.g., a specific computer serial number or switch port), allowing technicians to track the maintenance history of physical assets.
### Ticket 1: Restoring ServiceNow Integration Sync
- **Incident ID:** INC105512
- **Priority:** P3
- **Problem Statement:** "Workstations imaged via SCCM are not updating their serial numbers and asset ownership in ServiceNow."
- **Diagnostics:**
  1. Inspected the SCCM integration agent logs on the server.
  2. Found an API connection timeout error: `HTTP 401 Unauthorized` when trying to POST data to the ServiceNow endpoint.
  3. The service account credentials used for the integration had expired in Active Directory.
- **Resolution:** Reset the service account password in AD, updated the credential profile on the SCCM sync engine, and ran a manual sync test. The assets populated successfully.
### ServiceNow API Status Query (PowerShell)
```powershell
# Verify ServiceNow API endpoint is reachable
Invoke-RestMethod -Uri "https://company.service-now.com/api/now/table/incident" `
                  -Credential (Get-Credential) `
                  -Method Get
```
**Q1: What is a Configuration Item (CI) in a ticketing system?**
A: A Configuration Item is any component or asset that needs to be managed to deliver an IT service. This includes hardware (servers, laptops, routers), software licenses, active directories, and documentation. CIs are stored in the CMDB.

## Customizing Ticketing Workflows with Scripting
Modern ITSM systems (ServiceNow, Jira Service Desk) allow administrators to customize ticketing workflows to improve queue efficiency.

### Automation Rules & Escalation Workflows
- **Keyword Auto-Routing:** When a ticket contains terms like "printer" or "paper jam", the system routes the ticket to the local Site Support group automatically.
- **CMDB Relationship Mapping:** By linking configuration items (CIs), the system maps relationships. If a switch goes offline, the ticketing system automatically links all associated client PC alerts to a single master incident, preventing queue clutter.
- **SLA Breach Warnings:** Set up automated email warnings to alert support managers when a P2 ticket reaches 75% of its SLA resolution target without updates.
- **Automated Survey Triggers:** Once a technician closes a ticket, the system sends a customer satisfaction (CSAT) survey to measure team performance.

## Additional Pro-Tips for Ticket Workflows
To speed up resolution times, ticketing portals allow configuring 'Quick Templates' for common issues like password resets or hardware requests. These templates automatically populate description fields, assign the ticket to the correct group, set the impact and urgency, and log default troubleshooting steps. Additionally, integrating the ticketing system with email alerts ensures users receive updates automatically when their ticket changes status or requires input.

## Related Notes
- [[10-04 Asset Management]] - CMDB inventory databases
- [[05-02 Incident Management]] - Ticket workflows