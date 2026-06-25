---
tags: [desktop-support, sop, handover]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 13-09 Shift Handover Checklist

> [!abstract] Overview
> Standard Operating Procedure (SOP) for shift handovers in enterprise desktop support teams. This note details shift transition checklists, backlog audits, logging escalations, and writing handover mails.

---

## 1. What Is It? (Concept Explanation)
A shift handover is the structured transfer of operational responsibility and ticket information between outgoing and incoming support teams. It ensures that critical outages, VIP tickets, and pending tasks are resolved without interruptions.
*Seedha simple shabdon mein: Shift handover ka matlab hai ek shift se dusri shift ko responsibility hand over karna. Jab aapki duty khatam hoti hai, toh aap aane wale engineer ko batate hain ki kaun se severe issues chal rahe hain, kis network server par problem hai, aur kaun se tickets urgent hain taaki unki absence mein operations ruki na rahein. Iske liye ek handover email aur checklist update ki jati hai.*

---

## 2. Shift Handover Checklist SOP
Perform these checks at the end of every support shift:

- [ ] **Audit Open Tickets Backlog:**
  1. Open the ticketing dashboard (e.g., ServiceNow, Jira).
  2. Filter for open tickets assigned to your name or team queue.
  3. Sort by priority. Locate any P1 or P2 tickets that are unresolved.
- [ ] **Log Active Infrastructure Outages:**
  1. Note any active server, switch, or firewall outages reported during the shift.
  2. Confirm if the network engineering team has been notified and check the estimated resolution time.
- [ ] **Document VIP Ticket Status:**
  1. List outstanding requests for VIP executives (e.g., CEO, directors).
  2. Ensure notes contain exact statuses and clear next steps.
- [ ] **Verify Staging Lab Backlog:**
  1. Note how many laptops have been prepped and are ready for onboarding.
  2. Log any hardware failures encountered during imaging.
- [ ] **Write Handover Email:** Send a summary email to the incoming shift containing:
  - Critical open tickets (Ticket ID, User Name, Priority, Current Status).
  - Outage status updates.
  - Urgent pending tasks (e.g., "Image 5 Finance laptops for tomorrow morning").

---

## 3. Real-World Incident Tickets

### Ticket 1: SLA Breach due to Missing Handover Details
- **Incident ID:** INC110688
- **Priority:** P2 (ERP Access Down)
- **Problem Statement:** "Our branch manager cannot log into the ERP database. We logged a ticket 8 hours ago but have received no update."
- **Diagnostics:**
  1. Audited the ticket log history. Found the ticket was assigned to the day shift team.
  2. Day shift engineers verified local network settings, determined the database server was down, and planned to contact the database administrator.
  3. However, they went home at 5:00 PM without writing a handover log or notifying the night shift. The ticket lay inactive for 8 hours.
- **Resolution:** Apologized to the user, immediately escalated the database connection to the server team, and implemented a mandatory shift transition dashboard log to block unsigned handovers.

### Ticket 2: VIP Laptop Drop-off Communication Failure
- **Incident ID:** INC110705
- **Priority:** P3
- **Problem Statement:** "A Sales VP dropped off their laptop for screen repair at 4:00 PM. They were told it would be ready by 7:00 PM, but the night shift has no knowledge of it."
- **Diagnostics:**
  1. Inspected the build room. Found the repaired laptop on the technician bench.
  2. The screen replacement was completed by the day shift, but they forgot to update the ticket status or log it in the shift transition sheet.
- **Resolution:** Logged in, verified the laptop was operational, contacted the VP to collect the device, and updated the team templates to require a physical locker check during handovers.

---

## 4. Handover Email Template Reference
Use this standard format when emailing the incoming shift:
```text
Subject: IT Support Handover - Day Shift to Night Shift - [Date]

1. CRITICAL OUTAGES (P1/P2):
   - INC110821: File Server FS01 offline. Network team is investigating routing loops. ETA 11:30 PM.

2. VIP ESCALATIONS:
   - INC110833: CFO Laptop setup. Staging complete, ready for collection at IT Desk.

3. PENDING OPERATIONS BACKLOG:
   - Staging: 5 Dell Laptops imaged and boxed for HR onboarding tomorrow morning.
   - Active Queue: 12 open P3 tickets in queue.

4. SYSTEM HEALTH STATUS:
   - Print Server: Healthy.
   - Active Directory sync: Healthy.
```

---

## 5. Study & Interview Q&A
**Q1: Why is a structured shift handover critical in IT Operations?**
A: A structured handover prevents data loss, reduces ticket resolution times, and ensures continuous SLA compliance. Without it, incoming engineers waste time re-diagnosing active issues or miss critical outages, leading to business downtime.

**Q2: What do you do if a critical P1 outage is ongoing when your shift ends?**
A: I do not leave immediately. I contact the incoming shift lead, perform a live verbal walkthrough of the outage status, document all diagnostic steps in the active incident ticket, and verify the incoming engineer has taken ownership of the session before I log off.

**Q3: How do you handle a scenario where you locate an undocumented repaired laptop on the support bench with no ticket details?**
A: I search for the laptop serial number (Service Tag) in the ticketing database to check for open tickets. If no record is found, I contact the technician who was running the bench during the previous shift to get details, update the ticket history, and log the asset in the inventory shelf.

---

## Related Notes
- [[05-09 SLA Management & Reporting]] - Backlog targets
- [[00-Daily-Log-Template]] - Incident log checklists
- [[13-10 End-of-Day - Weekly Maintenance Checklist]] - Workspace checkouts