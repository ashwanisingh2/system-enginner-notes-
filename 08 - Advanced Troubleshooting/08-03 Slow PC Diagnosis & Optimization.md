---
tags: [desktop-support, troubleshooting, optimization]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 12 mins
---

# 08-03 Slow PC Diagnosis & Optimization

> [!abstract] Overview
> A diagnostic manual for isolating performance bottlenecks on slow workstations. This note details identifying resource exhaustion (CPU, Memory, Disk, Network) and optimization protocols.

---

## 1. What Is It? (Concept Explanation)
Slow PC performance is a common user complaint. It is caused by resource bottlenecks, software bloat, corrupted system configuration files, or failing physical hardware.
*Seedha simple shabdon mein bole toh: PC slow chalne ke 4 main reasons hote hain - CPU full ho jana, RAM bhar jana (jisse paging hoti hai), Disk speed slow hona (hard drive failure), ya background bloatware. Hamein Task Manager aur Resource Monitor se locate karna padta hai ki kaunsa resource limit par hai aur use optimize karna padta hai.*

---

## 2. Technical Deep-Dive: Resource Bottlenecks & Pagefile Thrashing
Isolating performance issues requires understanding resource metrics:

### Resource Bottleneck Indicators
- **CPU Spikes (100%):** Caused by runaway processes, active background virus scans, or outdated drivers looping in kernel space.
- **Memory Exhaustion & Paging:** When physical RAM is fully populated, Windows writes active application data to the hard drive in a file called `pagefile.sys` (this process is called **Paging**). Because hard drives are significantly slower than RAM, this causes the system to freeze or lag (known as **Thrashing**).
- **Disk Queue Length:** The number of read/write requests waiting in queue for the storage drive. A persistent disk queue length greater than 2 on a standard hard drive indicates a disk bottleneck or failing storage sector.

### Startup Bloatware & Services
Over time, applications register helper agents in the Windows startup folder and registry hives:
`HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
These agents consume CPU cycles and RAM from the moment the computer boots.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A senior financial analyst reports their workstation takes 15 minutes to boot, apps lag, and they get frequent "Your computer is low on memory" warnings when running Excel sheets.
- **Task:** Diagnose the resource bottleneck, optimize startup services, and restore device performance.
- **Action:**
  1. Opened Task Manager. Observed **Memory usage was at 96%** (out of 8GB installed) and **Disk active time was at 100%**.
  2. Switched to the **Resource Monitor** to check disk queue length and identify the high-memory processes.
  3. Identified that Chrome (30 tabs open) and multiple startup applications (Teams, OneDrive, Zoom, Spotify, CCleaner) were consuming 7.2 GB of memory, forcing the OS to thrash the pagefile on the mechanical HDD.
  4. Disabled non-essential startup applications using Task Manager's Startup tab and cleared corporate bloatware.
  5. Opened System Properties (`sysdm.cpl`), navigated to Advanced > Performance Settings > Advanced, and verified the Virtual Memory pagefile size was set to automatic.
  6. Recommended a physical hardware upgrade to 16GB RAM and a SSD swap since the user's workload required heavy multi-tasking.
  7. While waiting for hardware approval, cleaned temporary directories:
     ```cmd
     del /q /f /s "%temp%\*.*"
     ```
- **Result:** Boot times dropped from 15 minutes to 3 minutes, and the memory warnings resolved after disabling startup items. Performance fully stabilized after upgrading to an SSD and 16GB RAM.

---

## 4. Workstation Resource Metrics Table

| Resource | Bottleneck Threshold | Key Metric to Watch | Recommended Action |
|---|---|---|---|
| **CPU** | > 85% Persistent | Utilization percentage / Processes tab | End task of runaway process / check cooling. |
| **RAM** | > 90% Persistent | Available memory / Commit charge / Paging | Add physical RAM / disable startup apps. |
| **Disk** | > 5% Active (SSD) / > 80% (HDD) | Disk Active Time / Disk Queue Length | Swap mechanical HDD to SSD / run `chkdsk`. |
| **Network** | > 80% Interface Capacity | Send/Receive rates on interface | Close background sync apps (OneDrive/Dropbox). |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is the difference between Task Manager and Resource Monitor?**
A: **Task Manager** provides a quick, high-level overview of running apps and resource percentages. **Resource Monitor** provides granular detail, showing which specific files a process is reading, TCP connections, and individual disk queue lengths.

**Q2: How do I disable startup applications via registry?**
A: Press `Win + R`, type `regedit`, and navigate to `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`. Delete the registry strings of the third-party applications you do not want loading on startup.

**Q3: What does 100% active disk time with 0 MB/s read/write speed indicate?**
A: This is a common symptom of a failing hard drive. The disk controller is attempting to read bad sectors, causing it to hang and report 100% activity while transmitting zero data. Run `chkdsk /f /r` or check SMART status.

**Q4: How do I check disk SMART health status via command line?**
A: Open Command Prompt as Administrator and run: `wmic diskdrive get status`. If the status shows "Pred Fail", the hard drive is failing and must be replaced immediately.

---

## Related Notes
- [[08-04 System File & Disk Repair]] - Running SFC and CHKDSK
- [[08-07 Performance Monitoring Tools]] - Advanced metric logging
- [[12-01 Windows Keyboard Shortcuts (Complete)]] - Shortcut keys reference