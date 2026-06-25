---
tags: [desktop-support, windows, windows-update]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 9 mins
---

# 02-07 Windows Update & Patching

> [!abstract] Overview
> A guide to managing Windows Updates and patching. This note covers the differences between WSUS and Windows Update for Business, how to troubleshoot stuck updates, and commands to roll back updates.

---

## Concept Explanation: System Maintenance
Windows Update is Microsoft's service for providing operating system patches, security updates, and driver upgrades.
*Seedha simple shabdon mein bole toh: Windows Update security vulnerabilities ko patch karne aur system files ko secure rakhne ka tarika hai. Companies mein in updates ko central servers se test karke deploy kiya jata hai taaki koi naya update run-time applications ko crash na kar de. support engineers ko stuck updates aur rollbacks handle karne hote hain.*

---

## Enterprise Patching Architectures
In corporate networks, updates are controlled to manage network bandwidth and ensure stability:
- **WSUS (Windows Server Update Services):** A local server retrieves updates from Microsoft and distributes them to network clients. The IT team tests updates before approving them for deployment.
- **WUfB (Windows Update for Business):** Uses cloud-based group policies to organize clients into testing "rings" (e.g., IT gets updates on Day 1, HR on Day 5, Finance on Day 10).

---

## Support Scenario: Windows Update Stuck in a Boot Loop
- **Incident Description:** A user's laptop is stuck on startup displaying the message: "We couldn't complete the updates. Undoing changes. Don't turn off your computer." The system reboots and displays this screen again in a loop.
- **Troubleshooting Steps:**
  1. Boot the laptop into **Windows Recovery Environment (WinRE)** by turning it off and on three times during startup.
  2. Navigate to: **Troubleshoot** -> **Advanced Options** -> **Command Prompt**.
  3. Run the deployment image tool to check for pending updates:
     ```cmd
     dism /image:C:\ /get-packages
     ```
  4. Find the package marked "Install Pending" or "Rollback Pending".
  5. Remove the buggy package:
     ```cmd
     dism /image:C:\ /remove-package /packagename:Package_for_RollupFix~31bf3856ad364e35~amd64~~19041.1110.1.10
     ```
- **Resolution:** Removed the corrupted pending package, rebooted the PC to Windows, and cleared the update cache folder (`C:\Windows\SoftwareDistribution`) to allow the update to redownload correctly.

---

## SOP: Clearing the Windows Update Cache
If Windows Update is failing or stuck checking for updates, clear the local cache:
1. Open Command Prompt as Administrator.
2. Stop the update-related services:
   ```cmd
   net stop wuauserv
   ```
   ```cmd
   net stop bits
   ```
3. Navigate to the Windows directory and rename the cache folder:
   ```cmd
   rename C:\Windows\SoftwareDistribution SoftwareDistribution.old
   ```
4. Restart the update-related services:
   ```cmd
   net start wuauserv
   ```
   ```cmd
   net start bits
   ```
5. Open Windows Update Settings and check for updates to rebuild the folder.

---\n---

## Technical Deep-Dive: Windows Kernel & Service Operations
The Windows operating system relies on the **Windows NT Kernel** to manage hardware interactions, system security, and memory allocations.
- **User Mode vs. Kernel Mode:** Windows operates in two privilege modes. Applications (like Word or Chrome) run in User Mode, where they have restricted access to hardware. The OS kernel, drivers, and core services run in Kernel Mode, with direct access to system memory and hardware. A crash in User Mode only closes the app; a crash in Kernel Mode triggers a BSOD to prevent hardware damage.
- **Windows Boot Sequence:**
  1. **POST:** The system firmware (BIOS/UEFI) initializes hardware.
  2. **Boot Manager:** UEFI reads the EFI System Partition (ESP) and loads `bootmgr.efi`.
  3. **OS Loader:** Loads `winload.efi`, which starts kernel initialization and loads boot-start drivers.
  4. **Kernel Init:** Loads the NT Kernel (`ntoskrnl.exe`), initializes registry configurations, and launches the Session Manager Subsystem (`smss.exe`).
  5. **Logon Screen:** The Local Security Authority Subsystem (`lsass.exe`) starts, and display output shows the logon screen.

## Real-World OS Incident Tickets

### Ticket 1: Corrupted Windows Update Component Store
- **Incident ID:** INC108876
- **Priority:** P3
- **Problem Statement:** "Windows Update fails to install security patches, returning error code 0x800f081f. Trying to retry does nothing."
- **Diagnostics:**
  1. Inspected `%systemroot%\Logs\CBS\CBS.log`. Found multiple errors indicating missing files in the WinSxS directory (Component Store).
  2. Ran `sfc /scannow`, which returned: "Windows Resource Protection found corrupt files but was unable to fix some of them."
- **Resolution:**
  - Ran DISM targeting the online Windows image:
    `dism /online /cleanup-image /restorehealth`
  - DISM successfully repaired the corrupted files using Windows Update servers as the source.
  - Ran `sfc /scannow` again, which now completed with no errors. Restarted the PC and completed Windows Update.

### Ticket 2: Operating System Registry-Based Application Hang
- **Incident Description:** Excel crashes immediately upon startup for a finance user, even when launched in Safe Mode (`excel.exe /safe`).
- **Diagnostics & Resolution:**
  1. Checked Application Event Logs. Found Event ID 1000 (Application Error) pointing to `excel.exe` and faulting module `mso.dll`.
  2. Reinstalling Microsoft Office did not resolve the issue, indicating the problem was in the user profile configuration.
  3. Opened `regedit.exe` and navigated to the Excel user settings:
     `HKCU\Software\Microsoft\Office\16.0\Excel`.
  4. Renamed the `Excel` key to `Excel.old`.
  5. Relaunched Excel. The app recreated the registry keys with default values and started successfully.

## OS Support Interview Q&A Bank
**Q1: What is the purpose of the WinSxS folder in Windows?**
A: The WinSxS (Windows Side-by-Side) folder stores the Windows Component Store, which contains multiple versions of DLLs, drivers, and system files, allowing the OS to recover from file corruption and roll back updates.

**Q2: How do you troubleshoot a Windows machine that boots to a black screen with only a cursor?**
A: Press `Ctrl + Shift + Esc` to open Task Manager. If it opens, click File > Run new task, type `explorer.exe`, and check if the desktop loads. If Task Manager doesn't open, restart the PC in Safe Mode, uninstall recent updates, or roll back graphics drivers.

**Q3: What is the registry and how is it backed up?**
A: The registry is a hierarchical database of system settings. Windows backs up the registry automatically to System Restore points. Support engineers can manually back up keys by exporting them as `.reg` files in the Registry Editor.