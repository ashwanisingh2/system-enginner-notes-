---
tags: [desktop-support, windows, recovery]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 10 mins
---

# 02-10 System Recovery Tools

> [!abstract] Overview
> A guide to Windows Recovery Environment (WinRE) and system repair utilities. This note covers repairing boot configurations, running SFC/DISM, restore points, and restoring unbootable systems.

---

## Concept Explanation: Recovering Unbootable Systems
Windows includes built-in recovery tools to resolve boot issues, fix system corruption, and restore files from backups without reinstalling the operating system.
*Seedha simple shabdon mein bole toh: Jab computer windows load nahi kar pata aur black ya blue screen par freeze ho jata hai, tab hum Windows Recovery Tools ka use karte hain. Isme command prompt se tools chalana, dynamic system files ko repair karna, aur computer ko default state mein restore karna shaamil hai.*

---

## The Recovery Tools Matrix
Use this checklist to select the right tool for different system failures:

| Symptom | Recovery Tool | Key Commands / Steps |
|---|---|---|
| **Corrupted Windows Files** | System File Checker (SFC) | `sfc /scannow` |
| **Corrupted Component Store** | DISM Tool | `dism /online /cleanup-image /restorehealth` |
| **Boot Configuration Failure** | Bootrec Tool | `bootrec /fixmbr`, `bootrec /rebuildbcd` |
| **Driver Conflict after Install** | System Restore | Load a Restore Point from before the install |

---

## Support Scenario: Fix Windows Unbootable Loop after Update
- **Incident Description:** A user's desktop PC displays a blue screen error `INACCESSIBLE_BOOT_DEVICE` during startup and reboots in a loop.
- **Troubleshooting Steps:**
  1. Boot into Windows Recovery Environment (WinRE) by turning the PC off and on three times.
  2. Navigate to: **Troubleshoot** -> **Advanced Options** -> **Command Prompt**.
  3. Identify the active Windows partition letter (sometimes labeled `D:` in WinRE):
     ```cmd
     dir C:\
     dir D:\
     ```
  4. Run System File Checker targeting the offline directory paths:
     ```cmd
     sfc /scannow /offbootdir=D:\ /offwindir=D:\Windows
     ```
  5. Run DISM to scan the offline image registry for corruption:
     ```cmd
     dism /image:D:\ /cleanup-image /restorehealth
     ```
  6. Rebuild the Boot Configuration Data (BCD) files:
     ```cmd
     bootrec /rebuildbcd
     ```
- **Resolution:** Restarted the PC. The system restored successfully and loaded the Windows login screen.

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