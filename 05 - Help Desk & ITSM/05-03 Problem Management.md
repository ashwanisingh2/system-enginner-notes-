---
tags: [desktop-support, itsm, problem-management]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 05-03 Problem Management

> [!abstract] Overview
> A guide to Problem Management. This note covers the differences between incidents and problems, reactive vs. proactive management, Root Cause Analysis (RCA), and Known Error Databases (KEDB).

---

## 1. What Is It? (Concept Explanation)
While Incident Management focuses on restoring service quickly, **Problem Management** focuses on identifying the root cause of recurring incidents to prevent them from happening again.
*Seedha simple shabdon mein bole toh: Incident and Problem mein antar hai. Agar aapki bike ka tyre puncture ho jaye, toh tyre mein hawa bharna incident workaround hai. Par agar tyre baar-baar puncture ho raha hai, toh use check karna aur nail nikalna Problem Management hai. Hum root cause dhoondhte hain.*

---

## 2. Key Terms in Problem Management
- **Problem:** A cause of one or more incidents. The cause is usually unknown when the problem record is created.
- **Workaround:** A temporary fix that restores service without resolving the root cause (e.g., restarting a service daily).
- **Known Error Database (KEDB):** A repository of resolved problems and workarounds. Support engineers query the KEDB to resolve incidents quickly.
- **RCA (Root Cause Analysis):** The investigation process used to identify the underlying technical failure.

---

## 3. Real-World Scenarios

### Scenario 1: Resolving a Recurring Teams Audio Freeze
- **Incident Description:** Over two weeks, the service desk receives 35 identical tickets from different users reporting their Microsoft Teams audio freezes for 10 seconds during calls.
- **Problem Management Steps:**
  1. Open a **Problem Record** in ServiceNow and link all 35 incident tickets.
  2. Perform a **Root Cause Analysis (RCA)**:
     - Review system logs on the affected machines.
     - Note that all affected users use the same model of USB headset.
     - Check the firmware version of the headsets. They are running version `1.2.0`.
  3. Research the manufacturer's release notes. Version `1.2.0` has a known audio buffer overflow bug.
- **Resolution:**
  - Create a workaround entry in the KEDB: "To temporarily restore audio, unplug the USB headset and plug it back in."
  - Deploy the updated firmware version `1.3.1` using SCCM to all headsets.
  - Verify no new audio freeze incidents are logged, and close the parent problem record.

---
## 2. Technical Deep-Dive: Root Cause Analysis (RCA) & KEDB
Problem Management aims to prevent incidents from recurring by finding the root cause of issues:
- **Reactive Problem Management:** Initiated after recurring incidents occur.
- **Proactive Problem Management:** Initiated by analyzing trend logs and system metrics before incidents happen.
- **Ishikawa (Fishbone) Diagram:** A root cause analysis tool that groups potential causes of an issue into categories (People, Process, Technology, Environment).
- **Known Error Database (KEDB):** A repository of resolved problems and their workarounds, allowing support engineers to fix incidents faster.
### Ticket 1: RCA on Recurring Outlook Sync Outages
- **Incident ID:** PRB105312
- **Priority:** P3
- **Problem Statement:** "Multiple users in the Sales department report Outlook disconnected errors every afternoon."
- **Diagnostics & RCA:**
  1. Collected network traffic logs from affected workstations.
  2. Found that network traffic spiked exactly at 3:00 PM.
  3. Traced the spike to a local security agent that initiated a full virus scan at 3:00 PM daily, exhausting local client network sockets and dropping Outlook connections.
- **Resolution:** Re-configured the antivirus scan schedules to run staggered at night, resolving the sync issues. Documented the workaround in the KEDB.
### Root Cause Analysis (RCA) Action Plan
- [ ] **Define the Problem:** Clearly outline the symptoms and impact.
- [ ] **Collect Data:** Gather log files, timestamps, and system configurations.
- [ ] **Identify Root Cause:** Use the 5 Whys methodology to isolate the core failure.
- [ ] **Implement Solution:** Test and deploy a permanent fix.
- [ ] **Update KEDB:** Document the workaround and permanent resolution.
**Q1: What is a Known Error in ITIL Problem Management?**
A: A Known Error is a problem that has been analyzed, has a documented root cause, and has an active workaround or a planned permanent resolution. Known errors are logged in the KEDB to help support teams resolve associated incidents quickly.

## Brainstorming with the 5 Whys Methodology
When analyzing a recurring application crash, support engineers use the 5 Whys methodology to drill down to the root cause:

### Step-by-Step Root Cause Diagnostic Trace
- **1. Why did the user's CRM software freeze on launch?**
  *Answer:* The application database file (`local_cache.db`) was locked.
- **2. Why was the database file locked?**
  *Answer:* The local synchronization service process (`sync_agent.exe`) was hung, holding an active file handle.
- **3. Why did the sync agent process hang?**
  *Answer:* It was waiting for a network handshake response from the staging server.
- **4. Why was the staging server unresponsive?**
  *Answer:* The server IP address was changed during a routing upgrade.
- **5. Why did the sync agent still query the old IP?**
  *Answer:* The configuration file had a static IP mapped instead of using a dynamic DNS domain name, which is the root cause.

### Workarounds vs. Permanent Resolutions
- **Workaround:** Terminate the hung process and delete the lock file (resolves the incident temporarily, restoring service in 5 minutes).
- **Permanent Resolution:** Modify the sync agent configuration file to resolve using the DNS hostname (`sync.company.com`) and deploy the update via GPO, preventing future occurrences.

## Related Notes
- [[05-02 Incident Management]] - Incidents reporting
- [[05-08 Documentation & Knowledge Base]] - Log templates