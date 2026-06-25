---
tags: [desktop-support, security, EDR]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 11 mins
---

# 07-03 Antivirus & Endpoint Security

> [!abstract] Overview
> A comparison guide on endpoint protection platforms. This note covers signature-based detection vs. behavioral heuristics, managing EDR/AV agents, and configuring scanning exclusion directories.

---

## 1. What Is It? (Concept Explanation)
Endpoint Security involves protecting corporate workstations and laptops from malware and security exploits. Modern corporate environments utilize Endpoint Detection and Response (EDR) agents, which provide deeper visibility than legacy antivirus tools.
*Seedha simple shabdon mein bole toh: Antivirus humare PCs ka bodyguard hai. Purana AV sirf list check karta tha ki file blacklisted hai ya nahi. Naya EDR system dynamic hai - agar koi program background mein suspicious behaviour kare, jaise achanak se files encrypt karna, toh woh use instantly block kar deta hai.*

---

## 2. Technical Deep-Dive: AV Signatures vs. Behavioral EDR
Understanding how endpoint security agents analyze files:

### Legacy Signature Detection
Legacy antivirus agents scan file hashes (MD5/SHA-256) and compare them against a local database of known threats.
- **Limitation:** Fails to block "zero-day" exploits (brand-new malware that has not been cataloged yet).

### Behavioral EDR (Next-Gen AV)
Endpoint Detection and Response (EDR) systems (like Microsoft Defender for Endpoint, SentinelOne, or CrowdStrike Falcon) monitor process activity in memory:
- **Heuristics:** Analyzes what a program *does* rather than what it looks like. If a process attempts to inject code into another system process (like `lsass.exe`) or execute PowerShell scripts to bypass security, the EDR flags and isolates it.
- **Rollback:** Some EDR clients can roll back ransomware-encrypted files to their previous state using Windows shadow copies.

### Real-Time Scan Exclusions
Antivirus exclusions should be configured only when necessary, as they create blind spots. They are commonly applied to:
- Developer build directories (to speed up compilers).
- Large local databases (like SQL server directories) to prevent performance drops.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A software developer reports that their local workstation CPU usage is constantly at 100%, and compiling their code takes 20 minutes instead of 2 minutes. Task Manager shows `MsMpEng.exe` (Microsoft Defender) is consuming 85% CPU.
- **Task:** Diagnose the high CPU utilization, configure secure scan exclusions, and restore normal performance.
- **Action:**
  1. Identified that Defender's real-time engine was scanning thousands of temporary files generated in the developer's build folder (`C:\Staging\Build`) during compilation.
  2. Opened PowerShell as Administrator.
  3. Checked active Microsoft Defender preferences and exclusions:
     ```powershell
     Get-MpPreference | Select-Object ExclusionPath
     ```
  4. Confirmed the build directory was not excluded.
  5. Added the specific build folder to the Exclusion Path list:
     ```powershell
     Add-MpPreference -ExclusionPath "C:\Staging\Build"
     ```
  6. Verified the CPU usage of `MsMpEng.exe` dropped below 5% when running compiles.
- **Result:** Restored developer machine performance, reducing compile times back to 2 minutes while maintaining EDR protection on the rest of the system.

---

## 4. Antivirus & EDR Control Commands

### Force Microsoft Defender Update (PowerShell)
```powershell
# Update malware definitions immediately
Update-MpSignature
```

### Run Quick Malware Scan (PowerShell)
```powershell
# Run background quick scan
Start-MpScan -ScanType QuickScan
```

### Query Defender Exclusions (PowerShell)
```powershell
# List all excluded file types, paths, and processes
(Get-MpPreference).ExclusionPath
```

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is the difference between EDR and traditional antivirus?**
A: Traditional antivirus scans files on disk against a static signature database. EDR records system behavior, tracks process trees in real-time, detects fileless memory exploits, and allows administrators to isolate compromised systems from the cloud.

**Q2: Is it safe to exclude the entire C:\ Drive from real-time protection?**
A: Absolutely not. Excluding the root drive disables all file scanner security, allowing malware to execute undetected. Exclusions should be restricted to specific, non-user writable folders.

**Q3: How do I restart the Microsoft Defender service if it stopped?**
A: For security reasons, Microsoft Defender services cannot be stopped or restarted by local users or standard administrators via the Services console (this is blocked by Tamper Protection). It must be managed via the security portal or Group Policy.

**Q4: What is Microsoft Defender Tamper Protection?**
A: It is a security feature that prevents malicious apps or local users from changing Microsoft Defender Antivirus settings, disabling real-time protection, or turning off behavioral monitoring.

---

## Troubleshooting EDR Agent Connection Outages
If an EDR client agent (like SentinelOne) shows "Disconnected" status in the admin console:
1. Verify the local service is running using PowerShell:
   `Get-Service -Name SentinelAgent`
2. Test connection to the cloud console URL on port 443:
   `Test-NetConnection -ComputerName console.sentinelone.net -Port 443`
3. Check proxy settings if communicating over corporate firewalls.

## Related Notes
- [[07-02 Malware Types & Response]] - Threat types database
- [[10-02 Microsoft Intune Basics]] - Managing EDR configurations
- [[13-05 Malware Incident Response SOP]] - Malware containment SOP