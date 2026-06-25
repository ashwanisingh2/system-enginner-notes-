---
tags: [desktop-support, windows, event-viewer]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 9 mins
---

# 02-06 Windows Event Viewer

> [!abstract] Overview
> A guide to using Windows Event Viewer for troubleshooting. This note details log categories, event severity levels, critical Event IDs for desktop support, and procedures for parsing system errors.

---

## Concept Explanation: The Operating System Recorder
Windows Event Viewer is a built-in utility that records system, application, security, and setup logs. It acts as the system's flight recorder.
*Seedha simple shabdon mein bole toh: Event Viewer computer ka black box hai. Windows mein jab bhi koi service start hoti hai, crash hoti hai, ya password check galat hota hai, toh OS uski entry details ek database mein store kar deta hai. Jab computer achanak reboot ho ya software crash ho, hum Event Viewer mein aakar error logs check karte hain.*

---

## Core Log Categories and Severity Levels
The Event Viewer (`eventvwr.msc`) organizes logs into five main folders:
- **Application Logs:** Errors and warnings created by installed software (e.g., Outlook crash logs).
- **Security Logs:** Audit events like login attempts (successful and failed), privilege changes, and file access audits.
- **System Logs:** OS events, such as hardware driver failures, network connection drops, or system reboots.
- **Setup Logs:** OS installation and update records.

### Event Severity Levels
1. **Information:** A successful operation (e.g., a service started successfully).
2. **Warning:** An issue that doesn't immediately stop the system, but might cause future errors (e.g., disk running out of space).
3. **Error:** A critical failure that caused data loss or stopped a service (e.g., driver failed to load).
4. **Critical:** A severe system failure (e.g., sudden power failure or blue screen crash).

---

## Critical Event IDs for Support Engineers

| Event ID | Log Channel | Meaning | Action / Diagnosis |
|---|---|---|---|
| **41 (Kernel-Power)** | System | System rebooted without cleanly shutting down first. | Hard power loss, user held power button, or crash. |
| **1074 (User32)** | System | A clean system shutdown or reboot was initiated. | Identify the app or user that triggered the restart. |
| **4624 (Security)** | Security | An account logged on successfully. | Verify login type (e.g., Type 2=Local, Type 10=RDP). |
| **4625 (Security)** | Security | An account failed to log on. | Audit login failures to diagnose locked out accounts. |
| **7036 (Service Control Manager)** | System | A service changed its running state (stopped/started). | Track background service crashes. |

---

## Support Scenario: Diagnosing Random System Restarts
- **Incident Description:** A user complains that their desktop computer randomly restarts twice a day without displaying any error screens.
- **Troubleshooting Steps:**
  1. Open Event Viewer (`eventvwr.msc`).
  2. Expand **Windows Logs** and click on **System**.
  3. Click **Filter Current Log...** in the Actions pane on the right.
  4. In the Event ID field, type: `41, 1074`. Click OK.
  5. Inspect the matching events.
     - If you see Event 1074: Check the description. It will show the process that triggered the restart (e.g., `C:\Windows\system32\shutdown.exe` on behalf of `NT AUTHORITY\SYSTEM` for Windows Updates).
     - If you see Event 41 (Kernel-Power): This indicates sudden power loss.
- **Resolution:**
  - The System log showed multiple Event 41 events with no corresponding warnings beforehand.
  - Inspected the PC case and found the power cord connection to the power supply unit (PSU) was loose. Replaced the power cable and secured it to resolve the issue.

---

## Advanced Event Forwarding & Log Subscription
In corporate networks, security managers centralize log data using Windows Event Forwarding (WEF) and Windows Event Collector (WEC) architectures to identify network attacks.

### Forwarding and Collector Setup Steps
1. **Source Configuration:** Local computers run the WinRM service. A Group Policy is deployed to configure source machines to forward logs to a centralized collector server.
2. **Collector Setup:** The collector server runs the Windows Event Collector service and creates a Log Subscription (either source-initiated or collector-initiated).
3. **Log Storage:** Forwarded logs populate the hidden **ForwardedEvents** container in Event Viewer.

### Creating XML Custom Queries
Technicians construct XML custom queries in Event Viewer to filter complex logs quickly. For example, to find all locked accounts (Event ID 4740) and failed logins (Event ID 4625) in the Security log:
```xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[System[(EventID=4740 or EventID=4625)]]</Select>
  </Query>
</QueryList>
```
To clear log space on a client workstation using command line, run:
`wevtutil cl Application` (clears the Application log).

## Additional Pro-Tips for Event Analysis
To maximize log search performance, technicians configure custom views using the XML tab in Event Viewer rather than the GUI filter checkboxes. This allows filtering by complex logical patterns. Furthermore, Event Viewer logs can be exported directly to `.evtx` files and loaded on another computer for offline diagnostics. When troubleshooting network latency or service startups, checking the exact millisecond differences between boot event records in the System log helps pinpoint hardware initialization delays.

## Related Notes
- [[08-02 BSOD (Blue Screen of Death) Analysis]] - Windows debug options
- [[12-06 Event Viewer IDs Reference]] - Complete event ID table
- [[02-04 Windows Services & Processes]] - System background service management