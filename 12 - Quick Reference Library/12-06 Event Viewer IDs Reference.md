---
tags: [desktop-support, cheatsheet, event-viewer]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 12-06 Event Viewer IDs Reference

> [!abstract] Overview
> A diagnostic reference card compile of critical Windows Event IDs. This guide covers Event IDs across System, Security, Application, and Group Policy log directories, providing troubleshooting steps for each entry.

---

## 1. What Is It? (Concept Explanation)
The Windows Event Log service records system occurrences in centralized structured databases. Each entry is assigned an Event ID, which represents a unique operational state. Support engineers use these IDs to perform forensic analysis on system restarts, login failures, application crashes, and security breaches.
*Seedha simple shabdon mein: Event Viewer Windows ka flight recorder hai. Jab bhi system mein koi activity hoti hai (jaise user login, network connection drop, service start ya registry error), toh OS usse ek numeric code (Event ID) dekar system database mein save kar leta hai. IT support mein log filter lagakar in IDs ko check karne se issue ka root cause direct pata chal jata hai.*

---

## 2. Event Viewer Diagnostic Reference Card
Filter your log views using these essential Event IDs:

### 1. System Log: Boot & Power Operations
- **Event ID `12` (Source: Kernel-General):** The operating system started. Displays OS version, build details, and system boot time.
- **Event ID `1074` (Source: User32):** A clean shutdown or reboot was initiated by a user process or system update. The log description details the initiating user account (e.g., `SYSTEM` for updates) and reason codes.
- **Event ID `41` (Source: Kernel-Power):** The system rebooted unexpectedly without performing a clean shutdown sequence first. (Commonly caused by hard power cuts, crash screens, or user holding the power button).
- **Event ID `6008` (Source: Eventlog):** The previous system shutdown was unexpected. This entry is recorded on the subsequent boot.

### 2. Security Log: User Logon Audits
- **Event ID `4624` (Source: Security):** Successful account logon. The details tab lists the Logon Type:
  - *Logon Type 2:* Local interactive console logon (keyboard/mouse).
  - *Logon Type 3:* Network logon (accessing shared folders or printers).
  - *Logon Type 10:* Remote Desktop Connection (RDP).
- **Event ID `4625` (Source: Security):** Failed account logon attempt. Displays target username, domain, client IP address, and status code (e.g., `0xC000006A` for bad password).
- **Event ID `4740` (Source: Security):** A user account was locked out. The log details identify the exact computer name or client IP address where the lock attempts originated.

### 3. Application Log: Service & App Diagnostics
- **Event ID `1000` (Source: Application Error):** An application crashed. The log contains the faulting application name, version, and the faulting module library (e.g., `.dll` file).
- **Event ID `7036` (Source: Service Control Manager):** A service changed its state (stopped or started). Essential for verifying if services are running.
- **Event ID `7023` (Source: Service Control Manager):** A service terminated unexpectedly with an error exit code.

---

## 3. SOP: Diagnostic Filter Setup in Event Viewer
To quickly audit account lockouts:
1. Open Event Viewer (`eventvwr.msc`).
2. Navigate to **Windows Logs** -> **Security**.
3. In the Actions pane, click **Filter Current Log...**
4. In the text field `<All Event IDs>`, type `4740` and click OK.
5. Review the list. Double-click the latest event and read the **Caller Computer Name** field to locate the client machine sending bad authentication attempts.

---

## 4. Study & Interview Q&A
**Q1: What does Event ID 41 indicate and how do you investigate it?**
A: Event ID `41` (Kernel-Power) indicates that the computer restarted without shutting down cleanly first. It does not provide the direct cause. To investigate, I check the preceding events leading up to the crash. If I see Event ID `6008` or error codes, it implies a BSOD occurred. If there are no warning logs preceding the shutdown, it indicates a hardware-level power cut or a failing PSU.

**Q2: How do you use Event ID 4624 details to diagnose unauthorized RDP access?**
A: Event ID `4624` logs all successful logons. To check for remote access, I filter for this ID and check the **Logon Type** field. If I find **Logon Type 10**, it indicates a Remote Desktop Connection. I then audit the **Source Network Address** to verify the client IP address initiating the connection.

**Q3: How do you resolve a service crash flagged by Event ID 7023?**
A: Event ID `7023` indicates a service crashed. The event details contain the error code. I look up the service name, check its startup configurations in `services.msc`, and audit the application log for preceding Event ID `1000` crash details to check if a required DLL is missing or database is corrupted.

---

## Related Notes
- [[02-06 Windows Event Viewer]] - Log categories and operational channels
- [[12-05 BSOD Stop Codes Reference]] - Diagnostic codes
- [[02-04 Windows Services & Processes]] - Service configurations management