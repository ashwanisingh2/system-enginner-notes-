---
tags: [desktop-support, software, app-analysis]
created: 2026-06-25
status: Complete
difficulty: Advanced
estimated-read-time: 14 mins
---

# 06-08 Application Error Analysis

> [!abstract] Overview
> A guide to diagnosing application crashes and errors. This note covers parsing Event Viewer app logs, tracking DLL conflicts, and using Sysinternals tools to isolate software crashes.

---

## 1. What Is It? (Concept Explanation)
When an application crashes or fails to launch, the default error message is often vague (e.g., "Application has stopped working"). Troubleshooting requires parsing crash dumps, tracking DLL library conflicts, and auditing system resources.
*Seedha simple shabdon mein bole toh: Jab software crash ho aur normal troubleshooting (reinstall, reboot) kaam na kare, toh hum Event Viewer ke application logs check karte hain ya Process Explorer launch karte hain. Isse humein crash hone wali real DLL file aur fault code ka pata chalta hai.*

---

## 2. Tracking DLL Conflicts
Applications require Dynamic Link Library (DLL) files to run. If an application updates and overwrites a shared DLL file, or if a DLL is missing, other applications may crash:

- **Symptom:** Application fails to launch displaying: `MSVCR120.dll is missing` or `Runtime Error`.
- **Resolution:** Download and install the matching **Microsoft Visual C++ Redistributable** package (x86 or x64) from the official Microsoft site. This reinstalls the missing system DLL files.

---

## 3. Diagnostic Process: Isolating application crashes
If a key business application is crashing on launch:
1. Open Event Viewer (`eventvwr.msc`).
2. Go to **Windows Logs** -> **Application**.
3. Look for the red **Error** icon logged at the exact time of the application crash.
4. Open the error description and look for the following:
   - **Faulting application name:** (e.g., `excel.exe`).
   - **Faulting module name:** The file that caused the crash (e.g., `VBE7.DLL` or `mso.dll`).
   - **Exception code:** (e.g., `0xc0000005` - Access Violation error).
5. If the faulting module is a third-party add-in file, disable the add-in in the application settings or registry to resolve the crash.

---
## 2. Technical Deep-Dive: Event ID 1000 & DLL Dependencies
When a desktop application crashes on Windows, the OS generates log logs in Event Viewer:
- **Event ID `1000` (Application Error):** Logs application crashes. The event details contain:
  - **Faulting application name:** The executable file that crashed (e.g., `outlook.exe`).
  - **Faulting module name:** The library file that caused the crash (e.g., `mso.dll` or `kernel32.dll`).
  - **Exception code:** The hexadecimal memory error code (e.g., `0xc0000005` for Access Violation).
- **Process Monitor (ProcMon):** A Sysinternals utility used to trace active registry, file system, and DLL dependencies in real-time, helping locate missing configuration files.
### Ticket 1: Excel Crash on Launch due to Bad Add-in
- **Incident ID:** INC106811
- **Priority:** P3
- **Problem Statement:** "Excel closes immediately upon launch, showing no error messages on the screen."
- **Diagnostics:**
  1. Opened Event Viewer > Windows Logs > Application.
  2. Found Event ID `1000` pointing to `excel.exe` and faulting module `AdobePDFReader.dll`.
  3. Identified that a PDF reader plug-in was crashing Excel during startup.
- **Resolution:** Launched Excel in Safe Mode (`excel.exe /safe`), disabled the Adobe PDF Reader add-in, and restarted Excel normally. The app opened successfully.
### Launch Windows Process Monitor Tracing (CMD)
```cmd
:: Launch ProcMon tool silently logging to a backing file
procmon.exe /BackingFile C:\temp\procmon_trace.pml /Quiet
```
**Q1: How do you identify the cause of an application crash using Event Viewer?**
A: I open Event Viewer, go to Windows Logs > Application, and search for Event ID `1000`. I read the event details to identify the faulting application name and the faulting module library (e.g., a `.dll` file) causing the crash, which indicates the root cause.

## Analyzing Process Crash Dumps with ProcDump
When an application crashes without writing Windows Event Logs, support engineers use Sysinternals **ProcDump** to capture memory dumps:

### Execution Parameters
`procdump -e -ma outlook.exe C:\temp\outlook_crash.dmp`
- `-e` : Tells the utility to capture a dump only when an unhandled application exception occurs.
- `-ma` : Generates a full memory dump (including all active handles and thread stacks).

### Standard Crash Hex Exception Codes
- **`0xC0000005` (Access Violation):** The application attempted to read or write to a protected memory address it does not own. This is usually caused by bad code references or pointer issues.
- **`0xC0000374` (Heap Corruption):** The application corrupted its heap memory space, leading to crashes. Check for buggy plugins or DLL dependencies.

## Additional Pro-Tips for Memory Debugging
When an application crashes, Windows generates a crash dump file. If a standard dump file is not generated, technicians can configure the registry under `HKLM\Software\Microsoft\Windows\Windows Error Reporting\LocalDumps` to force the system to write crash files automatically. These files can then be analyzed using WinDbg to pinpoint the exact line of code or DLL dependency that triggered the crash, resolving deep application bugs.

## Related Notes
- [[02-06 Windows Event Viewer]] - System event viewer logs
- [[08-08 Sysinternals Suite for Support Engineers]] - Process Explorer configurations