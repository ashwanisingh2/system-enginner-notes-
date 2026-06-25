---
tags: [desktop-support, macos, basics]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 9 mins
---

# 02-12 macOS Basics for Support

> [!abstract] Overview
> A guide to supporting macOS endpoints in corporate environments. This note covers system navigation, keychain settings, diagnostic commands, and application installation.

---

## Concept Explanation: Supporting Apple Workstations
Many corporate organizations deploy macOS devices to design, marketing, and development teams. Understanding the macOS architecture (built on a Unix foundation) is essential for modern support engineers.
*Seedha simple shabdon mein bole toh: macOS visual design mein windows se alag lagta hai par iska backend Unix terminal hota hai. support engineers ko Keychain management, Application installation, aur network diagnostics seekhna hota hai.*

---

## Key macOS Administrative Tools
- **System Settings:** The centralized settings console (similar to Windows Control Panel).
- **Activity Monitor:** Real-time resource usage monitoring (similar to Windows Task Manager).
- **Keychain Access:** Built-in password manager. Stores account passwords, network credentials, and security certificates.
- **Terminal:** Command-line shell interface (running Zsh by default).

---

## Support Scenario: Resolving Keychain Password Loop
- **Incident Description:** A user changed their corporate domain password yesterday. Now, every time they boot their Mac, they receive multiple prompts saying: "The login keychain password does not match."
- **Troubleshooting Steps:**
  1. Open **Keychain Access** (via Spotlight Search `Cmd + Space`).
  2. Select **login** keychain in the sidebar.
  3. Click **Edit** in the top menu, then select **Change Password for Keychain "login"**.
  4. Enter the user's **Old Domain Password** as the current password.
  5. Enter the user's **New Domain Password** as the new password. Confirm it.
- **Resolution:**
  - If the user doesn't remember their old password, you must reset the keychain.
  - In Keychain Access settings, click **Reset Default Keychains**. This deletes stored passwords and creates a clean keychain using the new login credentials.

---

## Common macOS Terminal Commands
Use these commands in Zsh to query system details and troubleshoot:

```zsh
# Query Mac model name and serial number
system_profiler SPHardwareDataType | grep -E "Model Name|Serial Number"

# Check network IP configuration (Wi-Fi adapter is usually en0)
ipconfig getpacket en0

# Flush local DNS resolver cache on macOS Sonoma/Sequoia
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

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