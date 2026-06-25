---
tags: [desktop-support, linux, basics]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 9 mins
---

# 02-11 Linux Basics for Desktop Support

> [!abstract] Overview
> A quick reference guide to Linux administration for desktop support. This note covers basic commands, folder structures, permissions, network tools, and checking system logs.

---

## Concept Explanation: Supporting Linux Endpoints
Enterprise support environments often include Linux workstations, particularly in development, engineering, or research departments (commonly Ubuntu, RedHat, or Fedora).
*Seedha simple shabdon mein bole toh: Linux desktop interface windows se alag hota hai, aur isme lagbhag saara troubleshooting terminal se kiya jata hai. Ek support engineer ko terminal command chalana, folders browse karna, permission error theek karna, aur network tests run karna aana chahiye.*

---

## Linux Directory Structure Overview
- `/` : Root directory. All files and folders branch from here.
- `/etc` : Configuration files. (Like Windows Registry folders).
- `/var/log` : System logs directory. Contains auth and syslog logs.
- `/home/<username>` : User home directory. (Like `C:\Users\<username>`).
- `/bin` & `/sbin` : System binaries (compiled command line tools).

---

## Command Reference Card

### Directory Navigation & File Operations
```bash
# List directory contents with details (permissions, size)
ls -la

# Change current working directory
cd /var/log

# Copy a file to a backup destination
cp config.conf config.conf.bak

# Display file contents page-by-page
less syslog
```

### System Operations & Permissions
```bash
# Elevate shell permissions to run root administrative commands
sudo -i

# Change file permissions (755 = Owner R/W/X, Group R/X, Other R/X)
chmod 755 run_script.sh

# Change file ownership to a user and group
chown username:groupname file.txt
```

### Network Testing Tools
```bash
# Check current network adapter IP allocations
ip address show

# Trace network route paths
traceroute google.com

# Test network port connectivity
nc -zv 192.168.1.50 80
```

---

## Support Scenario: Troubleshooting Permissions Error on a Developer script
- **Incident Description:** A software developer reports they cannot run a deployment script they downloaded. They get a "Permission Denied" error in terminal, despite using `sudo`.
- **Troubleshooting Steps:**
  1. Open terminal on the user's Linux machine.
  2. Navigate to the script's directory and list details:
     `ls -l deploy.sh`
  3. Notice the permissions are set to `-rw-r--r--`. The execute bit (`x`) is missing.
- **Resolution:**
  1. Add execute permissions:
     `chmod +x deploy.sh`
  2. Check permissions again. They should read `-rwxr-xr-x`.
  3. Run the script: `./deploy.sh`. It executes successfully.

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