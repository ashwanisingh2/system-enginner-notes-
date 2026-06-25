---
tags: [desktop-support, troubleshooting, performance]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 11 mins
---

# 08-07 Performance Monitoring Tools

> [!abstract] Overview
> A comparison and implementation guide for Windows performance tracking tools. This note covers Task Manager, Resource Monitor, and configuring Performance Monitor (PerfMon) data collections.

---

## 1. What Is It? (Concept Explanation)
Performance Monitoring Tools are diagnostic consoles built into Windows used to measure system resources. They allow technicians to collect performance data, track running services, and locate hardware or software bottlenecks.
*Seedha simple shabdon mein bole toh: Performance monitoring tools PC ke health diagnostic tools hain. Task Manager se quick resources check hote hain. Resource Monitor se disk aur network details scan hoti hain. Aur PerfMon se hum metrics (jaise CPU ya RAM usage) ko background logs mein save kar sakte hain, taaki baad mein analyze kar sakein.*

---

## 2. Technical Deep-Dive: Task Manager vs. ResMon vs. PerfMon
Windows provides three primary utilities for tracking performance:

### 1. Task Manager (`taskmgr.exe`)
Best for quick, high-level troubleshooting.
- **Processes Tab:** Displays running applications, background processes, and active resource percentages.
- **Performance Tab:** Real-time graphs for CPU, Memory, Disk, Wi-Fi, Ethernet, and GPU.
- **Startup Tab:** Manage applications allowed to run at boot.

### 2. Resource Monitor (`resmon.exe`)
Best for real-time investigation of active resource conflicts.
- **CPU Tab:** Shows which processes are running active threads.
- **Memory Tab:** Shows physical memory allocation (Active, Standby, Free).
- **Disk Tab:** Shows active file write/read requests with paths. Useful for finding what file is bottlenecking disk speed.
- **Network Tab:** Lists TCP connections, active network processes, and listening ports.

### 3. Performance Monitor (`perfmon.msc`)
Best for advanced diagnostics and gathering historical baseline metrics.
- **Data Collector Sets:** Allows administrators to log system metrics over hours or days to a log file (`.blg`).
- **Critical Counters:**
  - `% Processor Time` (CPU utilization).
  - `Pages/sec` (frequency of virtual memory pagefile reads/writes).
  - `% Disk Time` (percentage of time the drive is busy).

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A design engineer reports their CAD workstation crashes randomly after working for 3-4 hours. A standard reboot fixes it temporarily. Task Manager does not show immediate errors when opened.
- **Task:** Track resource usage over a 4-hour window to identify the crash cause.
- **Action:**
  1. Opened **Performance Monitor** (`perfmon.msc`) on the user's workstation.
  2. Navigated to Data Collector Sets > User Defined. Right-clicked and created a new Data Collector Set.
  3. Added the following diagnostic counters to the collector:
     - `Process(*)\% Processor Time`
     - `Process(*)\Private Bytes` (tracks exact memory allocated to applications)
     - `Memory\Available MBytes`
  4. Scheduled the collector set to log data every 10 seconds to `C:\temp\perf_log.blg`.
  5. Instructed the user to work normally until the application crashed.
  6. Once the crash occurred at 3:00 PM, stopped the collector and opened the `.blg` file in Performance Monitor.
  7. Analyzed the graph and found that the CAD process `cadrender.exe` memory usage rose continuously from 1.2 GB to 15.8 GB (exhausting system RAM), confirming a **memory leak**.
- **Result:** Escalated the memory leak log to the software developer for a patch, and configured a script to restart the app every 3 hours as a temporary workaround.

---

## 4. Key Performance Counters & Thresholds

| PerfMon Counter Group | Specific Counter | Alert Threshold | Action on Breach |
|---|---|---|---|
| **Processor** | `% Processor Time` | > 85% for 10+ minutes | Check for looping threads / update BIOS. |
| **Memory** | `Available MBytes` | < 5% of physical RAM | Check for memory leaks / add RAM. |
| **Paging File** | `% Usage` | > 70% | Add physical RAM / resize pagefile. |
| **PhysicalDisk** | `Avg. Disk Queue Length` | > 2 per disk | Check storage drive health / swap to SSD. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What does "Standby Memory" mean in Resource Monitor?**
A: Standby memory contains cached data and code that is not actively in use but is kept in RAM so it can be loaded quickly if needed. If another application requires memory, Windows will immediately release Standby memory and allocate it.

**Q2: How do I launch Resource Monitor directly?**
A: Press `Win + R`, type `resmon`, and press Enter. Alternatively, you can click the "Open Resource Monitor" link at the bottom of the Performance tab in Task Manager.

**Q3: How do I analyze a .blg performance log file?**
A: You can open `.blg` files directly in Performance Monitor. Alternatively, you can convert them to a CSV file for analysis in Excel using the command: `relog input.blg -f CSV -o output.csv`.

**Q4: Why does a disk show 100% active time in Task Manager but low read/write speeds?**
A: This occurs when the hard drive controller is struggling to read corrupted sectors, causing the drive to hang and query the sector repeatedly. Run a disk check or replace the drive.

---

## Related Notes
- [[08-03 Slow PC Diagnosis & Optimization]] - Troubleshooting performance
- [[08-08 Sysinternals Suite for Support Engineers]] - Process Explorer guides
- [[12-06 Event Viewer IDs Reference]] - Event ID lookup