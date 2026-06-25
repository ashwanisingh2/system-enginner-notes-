---
tags: [desktop-support, software, printer]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 06-06 Printer & Printing Support

> [!abstract] Overview
> A guide to printer and printing support. This note covers the differences between local and network printers, mapping print servers, clearing print spooler crashes, and driver isolation.

---

## 1. What Is It? (Concept Explanation)
Printers are essential components in office environments. Troubleshooting printers involves managing print servers, driver configurations, physical connection layers, and spooler states.
*Seedha simple shabdon mein bole toh: Printers support engineers ke sabse common issues mein se ek hain. Printer offline show hona, print job spooler mein fansna, ya wrong driver installation se document layout kharab hona aam baat hai. Is note mein printing issues ko solve karne ke practical steps bataye gaye hain.*

---

## 2. Local vs. Network Printers
- **Local Printer:** Connected directly to the client PC using a USB cable. Configured on the local system port (e.g., `USB001`).
- **Network Printer:** Connected directly to the network switch via an Ethernet cable or Wi-Fi, using an IP address (e.g., `192.168.5.25`).
- **Print Server:** A central server that hosts printer drivers and manages printing queues. Users connect to printers shared on the server (e.g., `\\printserver01\Marketing-LaserPrinter`).

---

## 3. Real-World Scenarios

### Scenario 1: Printer Offline / Driver Port Mismatches
- **Incident Description:** A user reports they cannot print to their department printer. The status shows "Offline" in Windows, despite the printer being powered on and online for other users.
- **Troubleshooting Steps:**
  1. Open **Printers & Scanners** in Windows settings. Click the printer -> select **Printer Properties** -> go to the **Ports** tab.
  2. Click **Configure Port**.
  3. Check the configured IP address or hostname.
  4. Run a ping test to verify connection to the printer's IP address (e.g., `10.10.15.55`).
  5. The printer pings successfully, but the port in printer properties is pointing to an old IP address (`10.10.15.45`).
- **Resolution:**
  - Create a new TCP/IP port in the printer properties: Click **Add Port...** -> select **Standard TCP/IP Port** -> click New Port.
  - Input the printer's current IP address (`10.10.15.55`), complete the wizard, click Apply, and print a test page to verify.

---
## 2. Technical Deep-Dive: Print Spooler Architecture & Spool Files
The Windows Print Spooler service (`spoolsv.exe`) manages printing workflows:
- **Spool Files:** When a document is sent to print, the spooler generates two files in `%systemroot%\System32\Spool\PRINTERS\`:
  - `.SHD` (Shadow file): Contains job details (owner, printer name, priority).
  - `.SPL` (Spool file): Contains the raw print payload data.
- **SCM Service:** The Spooler service runs as a System service. If a print document contains invalid font characters, the spooler service can crash, freezing the print queue.
### Ticket 1: Print Spooler Crashes Continuously on PDF Print
- **Incident ID:** INC106622
- **Priority:** P3
- **Problem Statement:** "The printer is unresponsive, and trying to open the printer settings window returns error: 'The local print spooler service is not running'."
- **Diagnostics:**
  1. Opened Services console. Found the Print Spooler service was stopped.
  2. Tried to start the service. It started, but stopped again within 5 seconds.
  3. Checked the spool folder and found a large `.SPL` file containing a corrupted PDF print job.
- **Resolution:** Stopped the spooler service, deleted the `.SPL` and `.SHD` files in the printers folder, and restarted the service.
### Reset Print Spooler Queue via Command Line (CMD)
```cmd
:: Stop spooler, purge spool files, and restart spooler
net stop spooler
del /q /f /s "%systemroot%\System32\Spool\PRINTERS\*.*"
net start spooler
```
**Q1: How do you resolve a print queue that is stuck with a document showing "Deleting..." status?**
A: I open CMD as Administrator, stop the Print Spooler service using `net stop spooler`, navigate to `%systemroot%\System32\Spool\PRINTERS`, delete all `.spl` and `.shd` files, and restart the service using `net start spooler`.

## Advanced Network Printer Port Configurations
When mapping network printers in enterprise environments, support engineers configure advanced driver and port settings to ensure stability.

### RAW vs LPR Protocols
- **RAW Protocol:** The default printing protocol (uses TCP Port 9100). It has lower protocol overhead and is faster, making it ideal for standard office setups.
- **LPR (Line Printer Remote):** Uses TCP Port 515. LPR requires a queue name in settings, supports print server spooling, and is commonly used in UNIX/Linux networks.

### Troubleshooting Network Ports & Spooling
- **SNMP Status:** Disabling the "SNMP Status Enabled" check box in port configurations can resolve false offline printer errors caused by security firewalls blocking SNMP queries.
- **Printer Pools:** Enforce printer pooling when multiple identical printers are grouped. Users print to a single print queue, and the print server automatically routes the document to the first available printer, reducing office queue bottlenecks.

## Related Notes
- [[13-06 Printer Setup SOP (Local & Network)]] - Printing installation SOP
- [[12-08 Printer Error Codes Reference]] - Error codes key